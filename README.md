# Angular-s2i

# Purpose:
The purpose of this readme is to allow any reader who has access to docker, and openshift to build their angular web app on top of a Angular s2i builder. The reason for the usage of a community provided s2i is due to conflicts between the nodejs s2i that is provided by redhat and the angular web app. Same goes for Nginx s2i builder from red hat.

## Requiremnets:
Openhift 4 cluster
docker account
Terminal with OC cli installed
Sample Angular Web app. [Example](https://github.com/lholmquist/angular-web-app)
 
 
## Step 1 - Create a new namespace  
- angular-s2i is the name of our new namespace.
``` 
$ oc new-project angular-s2i
```


## Step 2 - Create a new project 
- We will be using the openshift command oc new-app to create a new application within our namespace.
- `--docker-image` is grabbing the s2i builder for angular applications from docker hub
- The `:latest` keyword signals docker to pull the most recent image 
- After the `~` we place our web app that can be found in a github repository
- `--name=` this is the name of the new application
``` 
$ oc new-app --docker-image=mprahl/s2i-angular-httpd24:latest~https://github.com/lholmquist/angular-web-app --name new-angular

example output:

--> Found container image 7207bfb (2 months old) from Docker Hub for "mprahl/s2i-angular-httpd24:latest"

    Angular 
    ------- 
    A Docker container based on centos/httpd-24-centos7 for building and running Angular web applications. Angular is a development platform for building mobile and desktop web applications using Typescript/JavaScript and other languages.

    Tags: builder, angular, httpd24

    * An image stream tag will be created as "s2i-angular-httpd24:latest" that will track the source image
    * A source build using source code from https://github.com/lholmquist/angular-web-app will be created
      * The resulting image will be pushed to image stream tag "new-angular:latest"
      * Every time "s2i-angular-httpd24:latest" changes a new build will be triggered

--> Creating resources ...
    imagestream.image.openshift.io "new-angular" created
    buildconfig.build.openshift.io "new-angular" created
    deployment.apps "new-angular" created
    service "new-angular" created
--> Success
    Build scheduled, use 'oc logs -f buildconfig/new-angular' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose service/new-angular' 
    Run 'oc status' to view your app.
```  


## step 3 - Check to see if your application is running
- This command retrieves all pods and their states in the current project
```
$ oc get pods

example output:

NAME                             READY   STATUS      RESTARTS   AGE
new-angular-1-build            1/1     Running     0          31s
```

## step 4 - Get the service and then expose it
- This next 2 commands are used to retrieve our service name to be used to expose it to the public. 
```
$ oc get svc
NAME            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)            AGE
new-angular   ClusterIP     xxx.xx.xxx.xxx   <none>        8080/TCP,8443/TCP   67s


$ oc expose svc/new-angular

route.route.openshift.io/new-angular exposed
```

## step 5 - Check to see if your application is running
- The following command will retrieve our url. We can copy and paste the url it gives us into a browser and see our application live.
```

$ oc get route

NAME            HOST/PORT                                              PATH   SERVICES        PORT       TERMINATION   WILDCARD
new-angular   new-angular-test-angular.<your url>       new-angular   8080-tcp   

```

If your using the sample app, it will look something like this.
![Screen Shot 2021-08-24 at 12 03 24 PM](https://user-images.githubusercontent.com/61709408/130659219-f7a21595-7fe1-4698-a7f8-36bfd45033eb.png)


