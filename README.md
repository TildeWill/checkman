# Checkman

Checkman runs your custom script(s) periodically and then updates system tray
icon. It could be used to check on CI build status, site http response, etc.

![](https://raw.github.com/cppforlife/checkman/master/screenshots/checkman.png)


# Installation

Install and open Checkman Mac app into `/Applications`
(backslash is for [overriding possible bash alias](http://en.wikipedia.org/wiki/Alias_(command\)#Overriding_aliases)):

    \curl https://raw.github.com/cppforlife/checkman/master/bin/install | bash -s

If you ever need to kill Checkman:

    killall Checkman


# Configuring Checkman via Checkfile(s)

* Any number of config files in `~/Checkman` directory

* Each config file can define any number of checks. Each check definition takes
  up a single line made of a name and a valid bash command separated by
  colon-space. Example checkfile with 3 checks:

    ```
    google.com: site.check http://www.google.com

    #-
    #- jenkins-ci.org builds
    jenkins_main_trunk: jenkins_build.check https://ci.jenkins-ci.org jenkins_main_trunk
    jenkins_lts_branch: jenkins_build.check https://ci.jenkins-ci.org jenkins_lts_branch
    ```

* Commands run relative to the containing checkfile.

* Symlinks are resolved. Suggested approach is to have per-project Checkfile
  with project specific checks and symlink to them from `~/Checkman` directory.
  e.g. `ln -s ~/workspace/my-project/Checkfile ~/Checkman/my-project`

* Hidden config files (prefixed with `.`) are skipped.

* Config files are reloaded when changes are saved.

* `#` can be used to comment.

* `#-` is used as a separator.

* `#- Some title` is used as a title separator.

* Tip: works great with `tee` to keep check's history
  e.g. `site.check http://site.com | tee -a tmp/site.log`


# Included check scripts

* `site.check <URL>` checks returned http response for 200 OK
  e.g. `site.check http://www.google.com`

* `vmc_apps.check <CC_TARGET_URL> <SPACE_ID> <APP_PREFIX>`
  checks that all apps are running
  e.g. `vmc_apps.check http://api.cc.com 37jsd-dsjf79-348jdd-fsa88 my-project`

* `jenkins_build.check <JENKINS_URL> <JOB_NAME>`
  checks specific Jenkins build status
  e.g. `jenkins_build.check https://ci.jenkins-ci.org jenkins_main_trunk`

* `test.check <RESULT> <CHANGING>` returns predefined result/changing
  e.g. `test.check true true`

* `airbrake.check <ACCOUNT_NAME> <API_TOKEN> <PROJECT_ID>`
  checks for recent errors
  e.g. `airbrake.check my-company 2a743rueigw87tegiofs7g 43878087`

Above scripts are located in `/Applications/Checkman.app/Contents/Resources/`.
Checkman makes these scripts available by appending stated path to PATH env
variable when running check commands.


# Building custom check scripts

Each check is expected to return following JSON:

    {
      // [Required]
      // Indicates whether check succeeded
      "result": <bool>,

      // [Optional]
      // Indicates whether result is in progress of being changed
      // e.g. CI build is in progress
      "changing": <bool>,

      // [Optional]
      // Url to open when check menu item is clicked
      "url": <string|null>,

      // [Optional]
      // List of additional details to show in the submenu
      "info": [
        [<string>, <string>] // Key-value pairs
      ]
    }

Check out included `scripts/` for examples.


## Debugging

Check's debugging window can be opened by `Option + click`-ing on its menu item.
Shown command/stderr/stdout will be updated as time passes. Refer to
[Debugging Tips](https://github.com/cppforlife/checkman/wiki/Debugging-Tips)
wiki page for more tips.


# Custom user settings

Change default 10 sec run interval for all checks:

    defaults write com.tomato.Checkman checkRunInterval -int 20


# Todos

* Audit CPU/memory usage
* Find permanent status images
* Run only single instance of the app
* Indicate when check info was last updated
* Limit number of checks running concurrently


# Thanks to

* Doc Ritezel
* Aram Price
