Service Manager (Official binaries)
===================================

The world's first cross-platform, open source (MIT or LGPL), programming AND scripting language-agnostic solution to system service development.

If you want to look at or change the source code or compile the binaries for yourself, go here:

https://github.com/cubiclesoft/service-manager-src

Features
--------

* Statically compiled targets for Windows, Mac, and Linux (32-bit and 64-bit).
* Natively supports these common service commands across all platforms:  install, uninstall, start, stop, restart, reload, status, waitfor, configfile, run, and custom actions.
* Also has a few per-platform options (e.g. win_priority, nix_user, nix_group).
* Nifty stuff:  Automatic restart if the service dies, handling system signals on appropriate platforms, etc.
* Has a liberal open source license.  MIT or LGPL, your choice.
* Sits on GitHub for all of that pull request and issue tracker goodness to easily submit changes and ideas respectively.

Intro Video
-----------

Watch the YouTube intro video:

[![Click to watch:  Introduction to Service Manager](https://img.youtube.com/vi/HobVSLiTDEI/0.jpg)](https://www.youtube.com/watch?v=HobVSLiTDEI "Click to watch:  Introduction to Service Manager")

It's got music and a pretty solid Aussie accent.  It is also only 5 minutes long.

How To Use Service Manager
--------------------------

Service Manager is a native executable that talks platform-specific language to manage services for the platform.  All of the available options can be viewed by running the executable without any options:

````
Syntax:

servicemanager.exe [options] action service-name
                   [[NotifyFile | CustomActionName CustomActionDescription]
                    ExecutableToRun [arguments]]

Use an English, hyphenated, lowercase name for 'service-name' to maximize
compatibility across platforms.


Actions:

install
        Install service/daemon.
        Requires NotifyFile and ExecutableToRun.
        'NotifyFile.stop' will be created when the process needs to stop.
        'NotifyFile.reload' will be created when the process needs to
        reload its configuration.

uninstall
        Uninstall service/daemon.

start
        Start service manager and service/daemon.

stop
        Stop service manager and service/daemon.
        Process is responsible for noticing the 'NotifyFile.stop' file
        and exiting in a timely manner.

restart
        Restart service/daemon.

reload
        Reload service/daemon.
        Process is responsible for deleting the 'NotifyFile.reload'
        file when the configuration has been reloaded.
        If the process is unable to support reloading, it must
        self-terminate.

waitfor
        Waits for the specified service manager service to start.
        Useful for handling dependencies inside the service itself.

status
        Retrieve basic service manager information.

configfile
        Outputs the location of the server manager configuration file.

run
        Runs the service.
        Most useful when -debug is specified, which runs the service as
        a simple console/terminal application.

addaction
        Adds a custom service manager action.
        Useful for actions such as 'configtest'.
        Requires CustomActionName, CustomActionDescription, and
        ExecutableToRun.


Options:

-pid=File
        Writes the process ID of this executable to the specified file.
        Install and run only.

-log=File
        Writes management information (start, stop, reload, etc.) to the
        specified log file.  You are on your own for log rotation.
        Install and run only.

-wait[=Milliseconds]
        The amount of time, in milliseconds, to wait for the process to
        complete an operation before forcefully terminating the process
        after creating the appropriate NotifyFile.
        The default behavior is to wait indefinitely.
        Install and run only.

-dir=StartDir
        Sets the starting directory of the new process.
        Install and run only.

-debug
        Enables console/terminal mode.  Run only.
````

For Windows, Service Manager is a bit redundant.  [SrvAny](https://support.microsoft.com/en-us/kb/137890) and [NSSM](https://nssm.cc/) are two commonly referenced tools.  Service Manager is a unified cross-platform interface - write once, run everywhere.

For Mac, Linux, and most *NIX variants, the binaries are accompanied with basic text files (e.g. 'servicemanager_nix.sysvinit').  These text files contain tokens that get replaced with the Service Manager executable location and the name of the service, which are then stored in system directories such as /etc/init.d/ to be run/processed during system startup so that the service starts at boot.

Service Manager stores configuration files in a global location on the system to provide reliable, consistent configuration of services.  You can find the location of the configuration file of any Service Manager enabled service by using the 'configfile' action.

How To Write A Service
----------------------

Your service has to watch for the existence of two files.  These notification files are '\[something\].reload' and '\[something\].stop'.  You specify, during installation, what the "\[something\]" is.  (If you use an SDK, there will be a reliable, standard mechanism.)  The easiest solution here is to use your service's directory and filename as the base.  Most languages can locate their own executables and scripts in the file system (e.g. PHP has \_\_FILE\_\_).

Ideally, there is a helper SDK for your favorite language.  If not, no worries.  The only real benefits of an SDK are ease of installation and removal and simple access to the service manager configuration for your service as well as selecting the correct executable to run for the platform.  You can always do a manual installation with slightly longer command-lines.

See an example service written in PHP using the PHP SDK [here](https://github.com/cubiclesoft/service-manager-src/blob/master/test_service.php).

How To Debug A Service
----------------------

One of the really nice features of Service Manager is that you don't need it to debug your service.  Just run your app normally from a command-line and use Ctrl+C to terminate it.

That said, sometimes things can work fine from a command-line but not so fine as a system service.  The Service Manager '-debug' 'run' action takes most of the same options as the 'install' action and then replicates some of the service environment without detaching from the terminal/shell/command prompt.  Doing this allows you to see debug output as it happens to figure out what is wrong.

Precompiled Binaries
--------------------

Precompiled binaries save the compilation step for select, popular architectures.  Currently, there are binaries for:

* Windows - Anything current, x86/x64 (i.e. 32-bit and 64-bit).
* Mac - x64 only (i.e. 64-bit Intel).
* Linux - x86/x64.

The binaries included in this repo are just meant to make most deployments easier since they aren't particularly big (~130KB each).

For other architectures (e.g. Linux on ARM), [building and installing from the source code](https://github.com/cubiclesoft/service-manager-src#building-and-installing-locally) is a good option.
