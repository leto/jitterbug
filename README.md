## Jitterbug : Cross Language Continuous integration for Git


### What is Jitterbug?

Jitterbug is written in Perl 5 and depends on various CPAN modules, such
as Moose, Dancer, DBIx::Class and others.

### Installing Jitterbug

    # You can also use Makefile.PL, but you will then have to manually 
    perl Build.PL
    # perl Makefile.PL

    # install missing dependencies
    ./Build installdeps

    # you can also use cpanm
    # cpanm --installdeps .

    # Look at config.yaml or example.yaml for how to configure your Jitterbug instance
    $EDITOR config.yaml

    # start the jitterbug Dancer app, which by default binds to port 3000
    perl jitterbug.pl

    # If you need to start it on a different port use -p
    perl jitterbug.pl -p 3001

In another terminal, deploy a DBIx::Class schema ( which is SQLite by default, 
change the values in config.yml to tweak) :

    perl scripts/jitterbug_db --config config.yml --deploy

Now add a post-receive hook to your github project that hits the /hook/ URL
on the server that the jitterbug Dancer app is running on, i.e.

    http://example.com:3001/hook/

Now you must start the builder, which actually clones a new git repo for
each task (this could be network-intensive) and actually runs the build
and test commands for each project.

    perl scripts/builder.pl -c config.yml

Now, when you commit to a project that has a Jitterbug post-receive hook,
the builder check periodically for a new task and build and test your
projects!

### Configuring Jitterbug

Take a look at example.yml, which uses all the advanced features of Jitterbug.
The easiest thing is to copy this file and tweak it to your needs.

Most of the configuration changes that you will make are in the "jitterbug" and
"plugins" config keys. Under the "jitterbug" key we have the keys "reports",
"build", "build_process", "options", "branches" and "projects". Both the
"reports" and "build" keys take a "dir" key, such as:

    jitterbug:
    reports:
        dir: /tmp/jitterbug
    build:
        dir: /tmp/build

This means that reports should be stored in /tmp/jitterbug and build directories will be stored
in /tmp/build.

