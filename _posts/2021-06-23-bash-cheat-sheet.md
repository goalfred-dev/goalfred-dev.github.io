---
layout: post
title: "Bash Cheat Sheet"
author: alfred
categories: [bash, cheat sheet]
featured: false
hidden: false
toc: true
---

#### References

[Bash One-Liners](http://www.bashoneliners.com/)

[Unix Bash Command One-Liners](https://itnext.io/increase-developer-productivity-with-unix-bash-command-one-liners-2973bccd7600)

### Including Other Scripts

```sh
# Includes
TOPDIR=${TOPDIR:-$(git rev-parse --show-toplevel)}
INCLUDE_BLD_DIR=${TOPDIR}/build-scripts
INCLUDE_DIR=${TOPDIR}/scripts/cicd

source ${INCLUDE_BLD_DIR}/env.sh
source ${INCLUDE_BLD_DIR}/docker-flow.sh
source ${INCLUDE_BLD_DIR}/kubernetes/env.sh
source ${INCLUDE_BLD_DIR}/debian-package/debian-flow.sh
source ${TOPDIR}/env.sh

source ${INCLUDE_DIR}/cicd-types.sh
source ${INCLUDE_DIR}/cicd-functions.sh
source ${INCLUDE_DIR}/cicd-get-branch-name.sh
source ${INCLUDE_DIR}/cicd-get-image-tag.sh
```

### Defining Types and Enums

```sh
export ENTCS_CICD_TEST_BRANCH_REGEX='^(test)(/v.+)'
export ENTCS_CICD_RELEASE_BRANCH_REGEX='^(release)(/v.+)'
export ENTCS_CICD_MASTER_BRANCH='master'

export ENTCS_CICD_BRANCH_TYPE_CI_DEV='ci-dev'
export ENTCS_CICD_BRANCH_TYPE_CI_MASTER='ci-master'
export ENTCS_CICD_BRANCH_TYPE_TEST='test'
export ENTCS_CICD_BRANCH_TYPE_RELEASE='release'

export ENTCS_CICD_REL_TYPE_NONE='none'
export ENTCS_CICD_REL_TYPE_ALPHA='alpha'
export ENTCS_CICD_REL_TYPE_BETA='beta'
export ENTCS_CICD_REL_TYPE_RC='rc'
export ENTCS_CICD_REL_TYPE_PROD='prod'
```

### Function Declaring

```sh
function cicd_determine_environment() {
  local branch_name=$1
  local image_tag=$2

  local rel_type=$(cicd_determine_rel_type $branch_name $image_tag)
  
  local env=$ENTCS_CICD_ENV_INTEGRATION

  case $rel_type in
    $ENTCS_CICD_REL_TYPE_ALPHA)
      env=$ENTCS_CICD_ENV_TEST
      ;;
    $ENTCS_CICD_REL_TYPE_BETA)
      env=$ENTCS_CICD_ENV_BETA
      ;;
    $ENTCS_CICD_REL_TYPE_RC)
      env=$ENTCS_CICD_ENV_STAGING
      ;; 
    $ENTCS_CICD_REL_TYPE_PROD)
      env=$ENTCS_CICD_ENV_PROD
      ;; 
    *)
      env=$ENTCS_CICD_ENV_INTEGRATION
  esac

  echo $env
}
```

### Function Calling

```sh
# Includes
INCLUDE_DIR=${TOPDIR:-$(git rev-parse --show-toplevel)}/scripts/cicd
source ${INCLUDE_DIR}/cicd-includes.sh

branch_name=$(cicd_get_branch_name)
image_tag=$(cicd_get_image_tag)

echo $(cicd_determine_environment $branch_name $image_tag)
```

### Function Test Script

```sh
source ./../cicd/cicd-types.sh
source ./../cicd/cicd-functions.sh

function test_determine_environment() {
  local branch_name=$1
  local image_tag=$2

  local env=$(cicd_determine_environment $branch_name $image_tag)
  echo "branch: $branch_name; image tag: $image_tag; environment: $env"
}

test_determine_environment 'feature/dev_branch' ''
test_determine_environment 'master' ''
test_determine_environment 'test/v1.0.2-alpha.1' '1.0.2-alpha.1'
test_determine_environment 'test/v1.0.2-beta.1' '1.0.2-beta.1'
test_determine_environment 'release/v1.0.2-rc.1' '1.0.2-rc.1'
test_determine_environment 'release/v1.0.2' '1.0.2'

echo ""

test_determine_environment 'feature/dev_branch' '1.0.2-alpha.1'
test_determine_environment 'master' '1.0.2-beta.1'
test_determine_environment 'test/v1.0.2-alpha.1' ''
test_determine_environment 'test/v1.0.2-beta.1' ''
test_determine_environment 'release/v1.0.2-rc.1' ''
test_determine_environment 'release/v1.0.2' ''
```

### Function Wrapping ENV Variable

```sh
function cicd_get_image_tag() {
  # Only consider it a 'production image' if IMAGE_VERSION_PROD is set from a tag on the current commit.
  image_tag=''
  if [[ -n "${TAGS_ON_CURRENT_COMMIT}" ]] ; then
    image_tag=${IMAGE_VERSION_PROD}
  fi
  echo $image_tag
}
```

### Cloning Set of Repos

```sh
entcs_repos=(client data-generator deploy-entcs-tsttwo device-configuration diagnostics domain entitlement-syncing notification telemetry-ingestion usage-api) 
eci_repos=(helm-enp-charts deploy-entcs-prdtwo)

function clone_repo() {
  local project=$1
  local repo_name=$2

  local repo_url="https://git.barco.com/scm/$project/$repo_name.git"
  local repo_path="./../../$repo_name"

  git clone ${repo_url} ${repo_path}

  pushd ${repo_path}

  git submodule update --init --recursive
  git submodule update --remote
  
  popd
}

for repo in ${entcs_repos[@]}; do
  project='entcs'
  clone_repo $project $repo
done

for repo in ${eci_repos[@]}; do
  project='eci'
  clone_repo $project $repo
done
```

### Publishing Docker Image

```sh
#!/usr/bin/env bash
set -e

# Includes
INCLUDE_DIR=${TOPDIR:-$(git rev-parse --show-toplevel)}/scripts/cicd
source ${INCLUDE_DIR}/cicd-includes.sh

# Determine environment
echo "cicd - Determining environment..."
branch_name=$(cicd_get_branch_name)
rel_image_tag=$(cicd_get_rel_image_tag)
env=$(cicd_determine_environment $branch_name $rel_image_tag)

# Latest image tag
latest_image_tag=${DEV_DOCKER_REGISTRY_HOST}/${DOCKER_NAME}:latest-${env}
echo "cicd - Latest image tag: $latest_image_tag"

# Pull newly built image
echo "cicd - Pulling newly built image ${DEV_DOCKER_IMAGE_FULL}"
docker pull "${DEV_DOCKER_IMAGE_FULL}"

# Tag pulled image
echo "cicd - Tagging pulled image with ${DEV_DOCKER_LATEST_IMAGE_FULL} with tag ${latest_image_tag}"
docker tag "${DEV_DOCKER_IMAGE_FULL}" "${latest_image_tag}"

# Push latest image
echo "cicd - Pushing latest image ${latest_image_tag}"
docker push "${latest_image_tag}"
```

### Returning a Boolean

```sh
# Includes
INCLUDE_DIR=${TOPDIR:-$(git rev-parse --show-toplevel)}/scripts/cicd
source ${INCLUDE_DIR}/cicd-includes.sh

branch_name=$(cicd_get_branch_name)
rel_image_tag=$(cicd_get_rel_image_tag)

dep_type=$(cicd_determine_dep_type $branch_name $rel_image_tag)

# Bash doesn't have a real boolean type so must use a convention.
# Here choice is to return lower case 'true' and 'false' strings.
if [[ $dep_type == $ENTCS_CICD_DEP_TYPE_CICD ]] ; then
  should_deploy='true'
else
  should_deploy='false'
fi

echo $should_deploy

# Example of usage in Jenkins pipeline script:

# stage('Deploy on K8S') {
#   environment {
#     ENTCS_CICD_ENVIRONMENT = sh (script: "./scripts/cicd/cicd-environment.sh", returnStdout: true).trim()
#     ENTCS_CICD_USAGE_API_IMAGE_VERSION = "${params.usage_api_image_version}"
#   }      
#   steps {
#       script {
#         def should_deploy = sh (script: "./scripts/cicd/cicd-should-deploy.sh", returnStdout: true).trim() 
#         if (should_deploy == 'true') {
#           sh 'build-scripts/kubernetes/deploy.sh'
#         } else {
#           echo 'Not deploying: it is an integration development branch (non-master branch) or the deployment type is CI/MD.'
#         }
#     }
#   }
```

### Showing Arrays of Variables

```sh
# Includes
INCLUDE_DIR=${TOPDIR:-$(git rev-parse --show-toplevel)}/scripts/cicd
source ${INCLUDE_DIR}/cicd-includes.sh

branch_name=$(cicd_get_branch_name)
rel_image_tag=$(cicd_get_rel_image_tag)

environment=$(cicd_determine_environment $branch_name $rel_image_tag)
release_type=$(cicd_determine_rel_type $branch_name $rel_image_tag)
branch_type=$(cicd_determine_branch_type $branch_name)

function display_varArray(){
  array=(${@})
  for avar in "${array[@]}"  ;
  do
  echo -e "${avar}:\t${!avar}"
  done
}

echo "----------------------------------------------------------------------------------------"
echo -e "CI/CD context variables: \n"
VARS=(environment release_type branch_name branch_type rel_image_tag)
display_varArray "${VARS[@]}"
echo "----------------------------------------------------------------------------------------"
```

### Syntax Case Switch

```sh
function cicd_determine_environment() {
  local branch_name=$1
  local rel_image_tag=$2

  local rel_type=$(cicd_determine_rel_type $branch_name $rel_image_tag)
  
  local env=$ENTCS_CICD_ENV_INTEGRATION

  case $rel_type in
    $ENTCS_CICD_REL_TYPE_ALPHA)
      env=$ENTCS_CICD_ENV_TEST
      ;;
    $ENTCS_CICD_REL_TYPE_BETA)
      env=$ENTCS_CICD_ENV_BETA
      ;;
    $ENTCS_CICD_REL_TYPE_RC)
      env=$ENTCS_CICD_ENV_STAGING
      ;; 
    $ENTCS_CICD_REL_TYPE_PROD)
      env=$ENTCS_CICD_ENV_PROD
      ;; 
    *)
      env=$ENTCS_CICD_ENV_INTEGRATION
  esac

  echo $env
}
```

### Syntax if-elif-fi

```sh
function cicd_determine_branch_type() {
  local branch_name=$1

  local branch_type=$ENTCS_CICD_BRANCH_TYPE_CI_DEV

  if [[ $branch_name =~ $ENTCS_CICD_TEST_BRANCH_REGEX ]]; then
    branch_type=$ENTCS_CICD_BRANCH_TYPE_TEST
  elif [[ $branch_name =~ $ENTCS_CICD_RELEASE_BRANCH_REGEX ]]; then
    branch_type=$ENTCS_CICD_BRANCH_TYPE_RELEASE
  elif [[ $branch_name == $ENTCS_CICD_MASTER_BRANCH ]]; then
    branch_type=$ENTCS_CICD_BRANCH_TYPE_CI_MASTER
  fi

  echo $branch_type
}

function cicd_determine_dep_type() {
  local branch_name=$1
  local rel_image_tag=$2

  local env=$(cicd_determine_environment $branch_name $rel_image_tag)
  local branch_type=$(cicd_determine_branch_type $branch_name)

  local dep_type=$ENTCS_CICD_DEP_TYPE_CIMD

  # if-fi example with AND condition
  if [[ ($env == $ENTCS_CICD_ENV_INTEGRATION) && ($branch_type == $ENTCS_CICD_BRANCH_TYPE_CI_MASTER) ]] ; then
    dep_type=$ENTCS_CICD_DEP_TYPE_CICD
  fi

  echo $dep_type
}

function cicd_determine_rel_type() {
  local branch_name=$1
  local rel_image_tag=$2

  local branch_type=$(cicd_determine_branch_type $branch_name)
  
  local rel_type=$ENTCS_CICD_REL_TYPE_NONE

  # Example of nested if-elif-fi
  if [[ -n $rel_image_tag ]] ; then
    if [[ $branch_type == $ENTCS_CICD_BRANCH_TYPE_TEST ]] ; then
      if [[ ($rel_image_tag == *"-$ENTCS_CICD_REL_TYPE_ALPHA."*) ]] ; then
        rel_type=$ENTCS_CICD_REL_TYPE_ALPHA
      elif [[ ($rel_image_tag == *"-$ENTCS_CICD_REL_TYPE_BETA."*) ]] ; then
        rel_type=$ENTCS_CICD_REL_TYPE_BETA
      fi
    elif [[ $branch_type == $ENTCS_CICD_BRANCH_TYPE_RELEASE ]] ; then
      if [[ ($rel_image_tag == *"-$ENTCS_CICD_REL_TYPE_RC."*) ]] ; then
        rel_type=$ENTCS_CICD_REL_TYPE_RC
      else
        rel_type=$ENTCS_CICD_REL_TYPE_PROD
      fi
    fi
  fi

  echo $rel_type
}
```

### Syntax String-Contains-String

```sh
  if [[ -n $rel_image_tag ]] ; then
    if [[ $branch_type == $ENTCS_CICD_BRANCH_TYPE_TEST ]] ; then
      # If the release image tag contains the string defined in ENTCS_CICD_REL_TYPE_ALPHA it is a alpha release.
      # Note that ENTCS_CICD_REL_TYPE_ALPHA is defined in an included script as: export ENTCS_CICD_REL_TYPE_ALPHA='alpha'
      if [[ ($rel_image_tag == *"-$ENTCS_CICD_REL_TYPE_ALPHA."*) ]] ; then
        rel_type=$ENTCS_CICD_REL_TYPE_ALPHA
      elif [[ ($rel_image_tag == *"-$ENTCS_CICD_REL_TYPE_BETA."*) ]] ; then
        rel_type=$ENTCS_CICD_REL_TYPE_BETA
      fi
    elif [[ $branch_type == $ENTCS_CICD_BRANCH_TYPE_RELEASE ]] ; then
      if [[ ($rel_image_tag == *"-$ENTCS_CICD_REL_TYPE_RC."*) ]] ; then
        rel_type=$ENTCS_CICD_REL_TYPE_RC
      else
        rel_type=$ENTCS_CICD_REL_TYPE_PROD
      fi
    fi
  fi
```

### Syntax String-Not-Null

```sh
  # If release image tag is filled...
  if [[ -n $rel_image_tag ]] ; then
    if [[ $branch_type == $ENTCS_CICD_BRANCH_TYPE_TEST ]] ; then
      if [[ ($rel_image_tag == *"-$ENTCS_CICD_REL_TYPE_ALPHA."*) ]] ; then
        rel_type=$ENTCS_CICD_REL_TYPE_ALPHA
      elif [[ ($rel_image_tag == *"-$ENTCS_CICD_REL_TYPE_BETA."*) ]] ; then
        rel_type=$ENTCS_CICD_REL_TYPE_BETA
      fi
    elif [[ $branch_type == $ENTCS_CICD_BRANCH_TYPE_RELEASE ]] ; then
      if [[ ($rel_image_tag == *"-$ENTCS_CICD_REL_TYPE_RC."*) ]] ; then
        rel_type=$ENTCS_CICD_REL_TYPE_RC
      else
        rel_type=$ENTCS_CICD_REL_TYPE_PROD
      fi
    fi
  fi
```

### Syntax Validate Regex

```sh
if [[ $branch_name =~ $ENTCS_CICD_TEST_BRANCH_REGEX ]]; then
  branch_type=$ENTCS_CICD_BRANCH_TYPE_TEST
elif [[ $branch_name =~ $ENTCS_CICD_RELEASE_BRANCH_REGEX ]]; then
  branch_type=$ENTCS_CICD_BRANCH_TYPE_RELEASE
elif [[ $branch_name == $ENTCS_CICD_MASTER_BRANCH ]]; then
  branch_type=$ENTCS_CICD_BRANCH_TYPE_CI_MASTER
fi
```

