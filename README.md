# sampleNod
Test sample for NodeJS with some basic commands to demonstrate the working of folder level caching of shippable.

This repository has 3 files and the functions of each file are as follows.
1. README.md - Contains an some basic conventions and guidelines to show the working of folder level caching.
2. Package.json - Installs all the libraries mentioned in dependencies list when we run npm install command from the root of this folder. The npm install command creates a new folder called node_modules which contains all the libraries mentioned in the dependency list.
3. Shippable.yml - Gives a list of command that runs during the CI step.

http://docs.shippable.com/ci_configure/ gives you a detailed introduction on how to configure your CI on shippable. So ignoring all the other sections in yml let's go straight to the caching part which is the heart of the discussion here.

1. cache: true tag will tell shippable that caching has to be enabled for this particular build and we will always look at this tag in the yml to see if we need to load anything from cache or not.

2. cache_dir_list:
  - $SHIPPABLE_BUILD_DIR/node_modules
  The cache_dir_list is an array of ABSOLUTE_PATH of the folders that needs to be cached. In the case of nodejs most of the time goes in installing all the node_modules so it makes sense to cache this particular folder. So here we give the absolute path of the node_modules directly. In a similar way if you are interested in caching some other folder, you can give the list of absolute paths to those folders.

Internal Implementation of cache.
1. All the users who have enabled cache have an s3 bucket created with their subscriptionId and all the projects that you cache will go into that bucket.
2. if we first see a cache: true option in your yml, we first try to load the cache file from the bucket, if the bucket exists we extract the files to their respective location and then contiue with the git sync and subsequent CI sections. After all the CI steps are over we look at the directories that needs to cached and create zip file and push it back to the bucket.
3. If we see a cache: false option in your yml we will override all the previous cache that was created and do not execute any of the cache related operations like push/pull of cache, restoring cache, caching of any form and [reset_minion].
4. The reset of cache can be done using [reset_minion] or by manually going to ui and click on reset cache button in the project settings. The next build that gets triggered after the reset will not use any old cache.

Please note that any errors during any of the caching steps are not treated as fatal errors.

#333


