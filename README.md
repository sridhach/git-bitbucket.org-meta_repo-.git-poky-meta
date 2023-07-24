# git-bitbucket.org-meta_repo-.git-poky-meta
Table of Contents
Prerequisites	1
Task	1

Prerequisites
•	Jenkins master
•	Jenkins agent with Ubuntu 20.04

Task
Need to create draft Jenkins Pipeline which does the build of source code by input Git Tag ($GIT_TAG_NAME), upload build artifacts to s3 ($S3_PATH), then notification is sent to some email ($EMAIL_LIST) in case of success/failure.
Source Code Build instruction was provided by Development team and located in the section below.
Pipeline draft should not be verified on real instance, draft skeleton of overall pipeline is enough (some details which can take time can be skipped).

Build instruction
Input Parameters:
$GIT_TAG_NAME
$EMAIL_LIST
$S3_PATH

Build steps (build steps were provided by development team): 

# Build should be done on Ubuntu 20.04.
# device_source.tar.gz is available at Ubuntu home dir “~/device_source.tar.gz”

# Setup tools from user home dir:
sudo apt-get install libxml2-utils curl gawk wget git-core gcc-multilib chrpath
sudo apt-get install build-essential
sudo apt-get install diffstat unzip texinfo
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt-get update
sudo apt-get install python3.7
sudo apt-get install -y python3-distutils
sudo ln -s /usr/bin/python3.7 /usr/bin/python
mkdir -p ~/.bin
PATH="${HOME}/.bin:${PATH}"
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/.bin/repo
chmod a+rx ~/.bin/repo
 

# Getting Build environment:

mkdir test_device_build; cd test_device_build
# copy device_source.tar.gz from initial location and extract
tar xvf device_source.tar.gz
cd apps_proc
repo init -u https://git.codelinaro.org/clo/le/le/manifest.git  -b release -m LE.UM.6.3.6.r1-02300-SDX65.0.xml
repo sync

# Getting source code from bitbucket and then checkout input $GIT_TAG_NAME:

git clone git@bitbucket.org:<meta_repo>.git poky/meta
git clone git@bitbucket.org:<source_repo>.git src/vendor/some-company
 
# checkout the input Git Tag ($GIT_TAG_NAME)
<commands were not provided by develop team>

# Build procedure:

#there are 3 build variants: “dev”, “qa” and “prod”
# below there are build procedure for all 3 variants

# 1 - dev:

export MACHINE=sdxlemur
export DISTRO=qti-distro-nogplv3-debug
source poky/qti-conf/set_bb_env.sh
bitbake productdev -c clean
bitbake productdev

# 2 - qa:
export MACHINE=sdxlemur
export DISTRO=qti-distro-nogplv3-debug
source poky/qti-conf/set_bb_env.sh
bitbake productqa -c clean
bitbake productqa

# 3 - prod:

export MACHINE=sdxlemur
export DISTRO=qti-distro-nogplv3-debug
source poky/qti-conf/set_bb_env.sh
bitbake product -c clean
bitbake product
 
#Output files to be uploaded to s3:

dev: apps_proc/build/dev_1.0.0.ipk
qa: apps_proc/build/qa_1.0.0.ipk
prod: apps_proc/build/prod_1.0.0.ipk
