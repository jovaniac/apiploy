pushapi
=======

A bash script to import an API proxy bundle to Apigee Edge, and 
optionally deploy the imported revision to a particular environment. 

This allows you to edit proxy files offline, and then import and deploy
proxies remotely. 

Installation
-----------

To install, copy the script to a directory. Make sure to chmod the script so that it has execute permissions. (chmod 755 pushapi) 


Usage
-----

    ./pushapi [options] <apiproxy directory>

The apiproxy directory must contain the definition of the API proxy. For
example, if the name of the directory is "myproxy" then the structure of
that directory ought to be like this:

    myproxy/
    myproxy/apiproxy/
    myproxy/apiproxy/myproxy.xml
    myproxy/apiproxy/proxies/
    myproxy/apiproxy/proxies/...
    myproxy/apiproxy/resources/
    myproxy/apiproxy/resources/...
    myproxy/apiproxy/policies/
    myproxy/apiproxy/policies/...
    myproxy/apiproxy/targets/
    myproxy/apiproxy/targets/...


The default action is to zip up the directory and import the resulting .zip file apiproxy bundle into an Edge organization.  You can also optionally deploy the api proxy as well. 

Options
------- 

     -h        display this message.
     -o org    the org to use.
     -e env    the environment to deploy to.
     -d        deploy the revision. (default: no deploy). Requires environment (-e)
     -n name   override the name of the apiproxy. (default: use the directory name)
     -S url    the base server url to use.
     -u creds  authn credentials for the API calls to the Edge management server.
     -x        undeploy any revs of the API in the environment; do not deploy a new rev.
     -X        undeploy & DELETE all revs of the API; do not deploy a new revision.
     -C num    delete all but last num revs of the API (if not deployed)
     -z        just zip the bundle; do not deploy a new revision.
     -q        quiet; decrease verbosity by 1
     -v        verbose; increase verbosity by 1

   
Examples 
--------

1. import an API called xyz-xform to the org named 'demo11':

    ./pushapi  -o demo11 xyz-xform

  The apiproxy will get a new revision. The output of the script will
  tell you the revision number. This will not deploy the proxy.

  In this case there should be a directory called xyz-xform in the
  current directory, and it should contain exactly one
  subdirectory, called apiproxy, and in there should be the requisite
  Apigee Edge proxy directory tree, containing all the files in your proxy.

2. import an API called xyz-xform to the org named 'demo11', and deploy it to the environment named 'test':

    ./pushapi -o demo11 -e test -d xyz-xform

  This will create a new revision of the API proxy.  The output of the script will tell you the revision number. 


3. import an API called xyz-xform to the org named 'demo11', and deploy it to the environment named 'test', with verbose output:

    ./pushapi -v -o demo11 -e test -d xyz-xform

  There is also a -q option for "quiet" operation.  By default, if you
  use two -q's then you get "silent" operation. If you use additional
  -v's then you get more verbosity.

4. just create the apiproxy zip bundle, don't import or deploy: 

    ./pushapi -z xyz-xform

5. undeploy any deployed revisions of the API proxy called abc from any environment in the demo11 org:

    ./pushapi  -o demo11 -x abc

6. undeploy any deployed revisions of the API proxy called abc from any environment in the demo11 org, and then delete all revisions:

    ./pushapi  -o demo11 -X abc

7. undeploy any deployed revisions of the API proxy called abc from the environment test in the demo11 org:

    ./pushapi  -o demo11 -e test -x abc

8. delete all but the most recent 10 revisions of the mayo1 proxy:

    ./pushapi  -o demo11 -C 10 mayo1



How it Works
------------

The sript uses the administrative APIs exposed by Apigee Edge to perform vrious actions, including:

 - checking for existing revisions of an API proxy
 - checking for the deployment status of revisions of an API proxy
 - undeploying revisions
 - deleting revisions (Warning! loss of data possible!)
 - importing bundles to Edge as a new revision of an apiproxy
 - deploying an imported api proxy

The script invokes these APIs using the cURL utility; it echoes the cURL commands as it runs
them, and checks the output status of those commands in order to handle
failures. You need to have curl available on the path.


Default Settings
----------------

The file .pushapi can contain settings for the org, env,
credentials, verbosity and other options. This file is dot-sourced from
within pushapi itself. This can eliminate the need for you to
specify all the options each time you invoke the script. Don't
put syntax errors in this settings file.

Example contents:

    org=demo11
    environment=test
    credentials=marjorie:secr3t
    verbosity=1

You can set quiet operation in the default settings file with:

    verbosity=1

Or specify silent operation with:

    verbosity=0

You should specify the file .pushapi in your .gitignore file to
avoid uploading secrets to the git repo.


Known Bugs
----------

None, as far as I know.


Testing
-------

What, me worry?


Contributing
------------

1. Fork it.
2. Create a branch (`git checkout -b my_improvement`)
3. Commit your changes (`git commit -am "Added Awesumo!"`)
4. Push to the branch (`git push origin my_improvement`)
5. Open a pull request.
6. Share and enjoy.
