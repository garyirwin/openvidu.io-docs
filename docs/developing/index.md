<h2 id="section-title">Developing OpenVidu</h2>
<hr>

- **[Prerequisites](#prerequisites)**
- **[Compiling OpenVidu Server](#compiling-openvidu-server)**
    - [Compiling OpenVidu Server dashboard](#compiling-openvidu-server-dashboard)
- **[Compiling OpenVidu Browser](#compiling-openvidu-browser)**
- **[Compiling OpenVidu WebComponent](#compiling-openvidu-webcomponent)**
- **[Running KMS](#running-kms)**
- **[Example: setup for development](#example-setup-for-development)**
- **[Example: setup for advanced development](#example-setup-for-advanced-development-share-the-app-through-your-network)**

---

## Prerequisites

| Dependency    | Check version   | Install                                 |
| ------------- | --------------- |---------------------------------------- |
| Java 8 JDK    | `java -version` | `sudo apt-get install -y openjdk-8-jdk` |
| Maven         | `mvn -v`        | `sudo apt-get install -y maven`         |
| Node          | `node -v`       | `sudo curl -sL https://deb.nodesource.com/setup_12.x | sudo bash -`<br>`sudo apt-get install -y nodejs` |

## Compiling OpenVidu Server

**1)** Clone OpenVidu parent repository and install local dependencies. **This is only necessary the first time you compile openvidu-server**

```bash
git clone https://github.com/OpenVidu/openvidu.git
cd openvidu
mvn -DskipTests=true clean install
```

**2)** Build openvidu-server

```bash
cd openvidu-server
mvn clean compile package
```

### Compiling OpenVidu Server dashboard

**1)** You need Angular to compile OpenVidu Server dashboard:

```bash
sudo npm install -g @angular/cli
```

**2)** Enter the Angular project and install NPM dependencies. **This is only necessary the first time you compile openvidu-server dashboard**

```bash
cd openvidu/openvidu-server/src/dashboard
npm install
```

**3)** Build the project. This npm command will copy the output files into the static files folder of openvidu-server. Next time you compile openvidu-server it will serve this last compilation of the dashboard.

```bash
npm run build
# For production compilation: npm run build-prod
```

<br>

---

## Compiling OpenVidu Browser

**1)** Clone OpenVidu parent repository and install openvidu-browser dependencies

```bash
git clone https://github.com/OpenVidu/openvidu.git
cd openvidu/openvidu-browser
npm install
```

**2)** Build openvidu-browser

```bash
npm run build
```

**3)** To install openvidu-browser as local NPM dependency

```bash
sudo npm link

# You can link this local dependency to any project that has openvidu-browser as
# dependency in its package.json, by calling "sudo npm link openvidu-browser"
```

**4)** To obtain static JavaScript files

```bash
VERSION=2.0.0 npm run browserify # Regular JS version
VERSION=2.0.0 npm run browserify-prod # Minified JS version

# Static files will be built in path ./static/js/
```

<br>

---

## Compiling OpenVidu WebComponent

**1)** Clone OpenVidu Call repository (openvidu-webcomponent is compiled from it) and install dependencies

```bash
git clone https://github.com/OpenVidu/openvidu-call.git
cd openvidu-call/front/openvidu-call
npm install
```

**2)** Build openvidu-webcomponent

```bash
npm run build:openvidu-webcomponent
```

If you want to test your OpenVidu WebComponent changes, you can clone openvidu-tutorials repository in the same path as openvidu-call repository, and the command `npm run build:openvidu-webcomponent` will automatically update the webcomponent files of [openvidu-webcomponent tutorial](tutorials/openvidu-webcomponent/). Make sure the version of webcomponent static files is the same as the imported ones in the HTML of the tutorial.

```bash
# Same path as "git clone https://github.com/OpenVidu/openvidu-call.git"
git clone https://github.com/OpenVidu/openvidu-tutorials.git
# Serve the tutorial. Build command of the webcomponent will update its files
http-server openvidu-tutorials/openvidu-webcomponent/web
```

<br>

---

## Running KMS

Run KMS in any OS that supports Docker with the following command:

```bash
docker run -d --name kms -p 8888:8888 kurento/kurento-media-server:latest
```

[Here](http://doc-kurento.readthedocs.io/en/stable/user/installation.html){:target="_blank"} you can check Kurento's official documentation.

<br>

---

## Example: setup for development

Here we show how to develop an Angular app (_openvidu-testapp_) with ***openvidu-browser*** and ***openvidu-server*** as local dependencies, waiting to be modified as you want. You can install Angular with `sudo npm install -g @angular/cli`.

**1)** Run [Kurento Media Server](#running-kms)

**2)** Clone repo:

```bash
git clone https://github.com/OpenVidu/openvidu.git
```

**3)** `openvidu/openvidu-browser/`

```bash
npm install
npm run build
sudo npm link
```

**4)** `openvidu/`

```bash
mvn -DskipTests=true install
```

**5)** `openvidu/openvidu-testapp/`

```bash
npm install
npm link openvidu-browser
ng serve
```

**6)** `openvidu/openvidu-server/`

```bash
mvn exec:java
```

*(or if you prefer you can just run the Java application in your favourite IDE)*

<br>

---

At these point, you can start modifying *openvidu-testapp*, *openvidu-browser* or *openvidu-server*.

 - **_openvidu-testapp_** :  the "ng serve" command will take care of refreshing the browser's page whenever any change takes place

 - **_openvidu-browser_** : after modifying any typescript file, you will need to run the following command to update your changes:

      */openvidu/openvidu-browser*:  `npm run build`

 - **_openvidu-server_** : after modifying any file, there is no other alternative but to re-launch the java application if you want to update your changes

      */openvidu/openvidu-server*: `mvn clean exec:java`

    *(or re-launch the Java application in your IDE. Some IDE's support automatic re-launch in response to changes)*

<br>

---

## Example: setup for advanced development (share the app through your network)

You can also use **different machines** in the **same network** to build a more advanced development environment, so you can test the application in different devices at the same time. It's very similar to the process outlined above:

Run exactly the same commands as in the previous section, but:

1. On step **5)** extend `ng serve` command with:

        ng serve --host 0.0.0.0 --ssl true

2. On step **6)** extend `mvn exec:java` command with:

        mvn -Dopenvidu.publicurl=https://HOST_LOCAL_IP:4443/ exec:java
        # Being HOST_LOCAL_IP the local IP that your machine serving the app has in your LAN network

This way we first tell AngularCLI to serve our app through https and to expose the port in our LAN network, and secondly we set OpenVidu Server public url to the public IP of the machine in our LAN network. This way our devices will be able to reach it as long as they are connected to the same network.

To connect to the application from any device, be sure to be connected to the same LAN network and navigate to `https://HOST_LOCAL_IP:4200` (being `HOST_LOCAL_IP` the local IP that your machine serving the app has in your LAN network).
