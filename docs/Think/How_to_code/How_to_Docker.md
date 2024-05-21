## What is a container?

A container is an isolated environment for your code. This means that a container has no knowledge of your operating system, or your files. It runs on the environment provided to you by Docker Desktop. Containers have everything that your code needs in order to run, down to a base operating system. You can use Docker Desktop to manage and explore your containers.

In this walkthrough, you'll view and explore an actual container in Docker Desktop.

### Step 1: Set up the walkthrough

The first thing you need is a running container. Use the following instructions to run a container.

1. Open Docker Desktop and select the search.
2. Specify `docker/welcome-to-docker` in the search and then select **Run**.
3. Expand the **Optional settings**.
4. In **Container name**, specify `welcome-to-docker`.
5. In **Host port**, specify `8088`.
    
    ![Specifying host port 8088](https://docs.docker.com/guides/walkthroughs/images/getting-started-setup.webp?w=500&border=true)
    
6. Select **Run**.

### Step 2: View containers on Docker Desktop

You just ran a container! You can view it in the **Containers** tab of Docker Desktop. This container runs a simple web server that displays a simple website. When working with more complex projects, you'll run different parts in different containers. For example, a different container for the frontend, backend, and database. In this walkthrough, you only have a simple frontend container.

### Step 3: View the frontend

The frontend is accessible on port 8088 of your local host. Select the link in the **Port(s)** column of your container, or visit [http://localhost:8088](http://localhost:8088/) in your browser to view it.

![Accessing container frontend from Docker Desktop](https://docs.docker.com/guides/walkthroughs/images/getting-started-frontend.webp?border=true)

### Step 4: Explore your container

Docker Desktop lets you easily view and interact with different aspects of your container. Try it out yourself. Select your container and then select **Files** to explore your container's isolated file system.

![Viewing container details in Docker Desktop](https://docs.docker.com/guides/walkthroughs/images/getting-started-explore-container.webp?border=true)

### Step 5: Stop your container

The `welcome-to-docker` container continues to run until you stop it. To stop the container in Docker Desktop, go to the **Containers** tab and select the **Stop** icon in the **Actions** column of your container.

![Stopping a container in Docker Desktop](https://docs.docker.com/guides/walkthroughs/images/getting-started-stop.webp?border=true)

### Summary

In this walkthrough, you ran a pre-made image and explored a container. In addition to running pre-made images, you can build and run your own application as container.

## How do I run a container?

In this walkthrough, you'll learn the basic steps of building an image and running your own container. This walkthrough uses a sample Node.js application, but it's not necessary to know Node.js.

![Running an image in Docker Desktop](https://docs.docker.com/guides/walkthroughs/images/getting-started-run-intro.webp?w=450&border=true)

### Step 1: Get the sample application

If you have git, you can clone the repository for the sample application. Otherwise, you can download the sample application. Choose one of the following options.

Use the following command in a terminal to clone the sample application repository.

```console
$ git clone https://github.com/docker/welcome-to-docker
```

### Step 2: View the Dockerfile in your project folder

To run your code in a container, the most fundamental thing you need is a Dockerfile. A Dockerfile describes what goes into a container. This sample already contains a `Dockerfile`. For your own projects, you'll need to create your own `Dockerfile`. You can open the `Dockerfile` in a code or text editor and explore its contents.

### Step 3: Build your first image

You always need an image to run a container. In a terminal, run the following commands to build the image. Replace `/path/to/welcome-to-docker/` with the path to your `welcome-to-docker` directory.

```console
$ cd /path/to/welcome-to-docker/
```

```console
$ docker build -t welcome-to-docker .
```

In the previous command, the `-t` flag tags your image with a name, `welcome-to-docker` in this case. And the `.` lets Docker know where it can find the Dockerfile.

Building the image may take some time. After your image is built, you can view your image in the **Images** tab in Docker Desktop.

### Step 4: Run your container

To run your image as a container:

1. In Docker Desktop, go to the **Images** tab.
2. Next to your image, select **Run**.
3. Expand the **Optional settings**.
4. In **Host port**, specify `8089`.
    
    ![Specifying host port 8089](https://docs.docker.com/guides/walkthroughs/images/getting-started-run-image.webp?w=500&border=true)
    
5. Select **Run**.

### Step 5: View the frontend

You can use Docker Desktop to access your running container. Select the link next to your container in Docker Desktop or go to [http://localhost:8089](http://localhost:8089/) to view the frontend.

![Selecting the container link](https://docs.docker.com/guides/walkthroughs/images/getting-started-frontend-2.webp?border=true)

### Summary

In this walkthrough, you built your own image and ran it as a container. In addition to building and running your own images, you can run images from Docker Hub.

## Run Docker Hub images

You can share and store images in Docker Hub ([http://hub.docker.com](http://hub.docker.com/)). Docker Hub has over 100,000 images created by developers that you can run locally. You can search for Docker Hub images and run them directly from Docker Desktop.

### Step 1: Search for the image

You can search for Docker Hub images on Docker Desktop. To search for the image used in this walkthrough:

1. Open Docker Desktop and select the search.
2. Specify `docker/welcome-to-docker` in the search.

![Search Docker Desktop for the welcome-to-docker image](https://docs.docker.com/guides/walkthroughs/images/getting-started-search.webp?w=650&border=true)

### Step 2: Run the image

To run the `docker/welcome-to-docker` image:

1. After finding the image using search, select **Run**.
2. Expand the **Optional settings**.
3. In **Host port**, specify `8090`.
    
    ![Specifying host port 8090](https://docs.docker.com/guides/walkthroughs/images/getting-started-run.webp?w=500&border=true)
    
4. Select **Run**.

> **Note**
> 
> Many images hosted on Docker Hub have a description that highlights what settings must be set in order to run them. You can read the description for the image on Docker Hub by selecting the image name in the search or by searching for the image directly on [https://hub.docker.com](https://hub.docker.com/).

### Step 3: Explore the container

That's it! The container is ready to use. Go to the **Containers** tab in Docker Desktop to view the container.

![Viewing the Containers tab in Docker Desktop](https://docs.docker.com/guides/walkthroughs/images/getting-started-view.webp?border=true)

### Summary

In this walkthrough, you searched for an image on Docker Hub and ran it as a container. Docker Hub has over 100,000 more images that you can use to help build your own application.

## Run multi-container applications

If you've already completed the [[#How do I run a container?]] walkthrough, you learned that you must start each container individually. Imagine how great it would be if a tool could start multiple containers with a single command. That tool is Docker Compose.

### Step 1: Get the sample application

If you have git, you can clone the repository for the sample application. Otherwise, you can download the sample application. Choose one of the following options.

Use the following command in a terminal to clone the sample application repository.

```console
$ git clone https://github.com/docker/multi-container-app
```

The sample application is a simple todo application built using ExpressJS and Node.js. The application saves all todos in a MongoDB database. You don't need to know any of these technologies to continue with the walkthrough.

![The sample app architecture](https://docs.docker.com/guides/walkthroughs/images/getting-started-multi-container.webp?w=450&border=true)

### Step 2: Dig into the Compose file

View the files of the sample application. Notice that it has a `compose.yaml` file. This file tells Docker how to run your application. Open the `compose.yaml` file in a code or text editor to view what it contains.

### Step 3: Run the application

To run the multi-container application, open a terminal and run the following commands. Replace `/path/to/multi-container-app/` with the path to your application's directory.

```console
$ cd /path/to/multi-container-app/
```

```console
$ docker compose up -d
```

In the previous command, the `-d` flag tells Docker Compose to run in detached mode.

### Step 4: View the frontend and add todos

In the **Containers** tab of Docker Desktop, you should now have an application stack with two containers running (the todo-app, and todo-database).

To view the frontend:

1. In Docker Desktop, expand the application stack in **Containers**.
2. Select the link to port **3000** in the **Port(s)** column or open [http://localhost:3000](http://localhost:3000/)⁠.

Add some todo tasks in the frontend, and then open [http://localhost:3000](http://localhost:3000/) in a new browser tab. Notice that the tasks are still visible.

### Step 5: Develop in your containers

When developing with Docker, you may need to automatically update and preview your running services as you edit and save your code. You can use Docker Compose Watch for this.

To run Compose Watch and see the real-time changes:

1. Open a terminal and run the following commands. Replace `/path/to/multi-container-app/` with the path to your application's directory.
    
    ```console
    $ cd /path/to/multi-container-app/
    ```
    
    ```console
    $ docker compose watch
    ```
    
2. Open `app/views/todos.ejs` in a text or code editor, then change the text on line 21.
3. Save the changes in `app/views/todos.ejs`.
4. View your application at [http://localhost:3000](http://localhost:3000/) to see the changes in real-time.

### Step 6: Delete everything and start over

Having your configuration stored in a Compose file has another advantage, you can easily delete everything and start over.

To delete the application stack:

1. Open the **Containers** tab of Docker Desktop
2. Select the Delete icon next to your application stack.

![Deleting the application stack](https://docs.docker.com/guides/walkthroughs/images/getting-started-delete-stack.webp?border=true)

After you delete the application stack, follow the steps from [[#Run Docker Hub images]] to run the application again. Note that when you delete the containers and run them again, any todos that you created don't persist.

### Summary

In this walkthrough, you ran a multi-container application with Docker Compose. You also learned how to develop in containers and how to delete the application stack along with all of the data.

## Persist container data

This walkthrough shows you how to persist data between containers. To better understand some concepts in this walkthrough, complete the [[#Run multi-container applications]] walkthrough first.

Docker isolates all content, code, and data in a container from your local filesystem. When you delete a container, Docker deletes all the content within that container.

![Data isolation diagram](https://docs.docker.com/guides/walkthroughs/images/getting-started-isolation.webp?w=400)

Sometimes, you may want to persist the data that a container generates. To do this, you can use volumes.

### Step 1: Get the sample application

If you have git, you can clone the repository for the sample application. Otherwise, you can download the sample application. Choose one of the following options.

Use the following command in a terminal to clone the sample application repository.

```console
$ git clone https://github.com/docker/multi-container-app
```

### Step 2: Add a volume to persist data

To persist data after you delete a container, use a volume. A volume is a location in your local filesystem, automatically managed by Docker Desktop.

![Volume diagram](https://docs.docker.com/guides/walkthroughs/images/getting-started-volume.webp?w=400)

To add a volume to this project, open the `compose.yaml` file in a code or text editor, and then uncomment the following lines.

```yaml
todo-database:
    # ...
    volumes:
      - database:/data/db

# ...
volumes:
  database:
```

The `volumes` element that is nested under `todo-database` tells Compose to mount the volume named `database` to `/data/db` in the container for the todo-database service.

The top-level `volumes` element defines and configures a volume named `database` that can be used by any of the services in the Compose file.

### Step 3: Run the application

To run the multi-container application, open a terminal and run the following commands. Replace `/path/to/multi-container-app/` with the path to your application's directory.

```console
$ cd /path/to/multi-container-app/
```

```console
$ docker compose up -d
```

### Step 4: View the frontend and add todos

In the **Containers** tab of Docker Desktop, you should now have an application stack with two containers running (the todo-app, and todo-database).

To view the frontend and add todos:

1. In Docker Desktop, expand the application stack in **Containers**.
2. Select the link to port **3000** in the **Port(s)** column or open [http://localhost:3000](http://localhost:3000/)⁠.
3. Add some todo tasks in the frontend.

### Step 5: Delete the application stack and run new containers

Now, no matter how often you delete and recreate the containers, Docker Desktop persists your data and it's accessible to any container on your system by mounting the `database` volume. Docker Desktop looks for the `database` volume and creates it if it doesn't exist.

To delete the application stack:

1. Open the **Containers** tab of Docker Desktop
2. Select the Delete icon next to your application stack.

![Deleting the application stack](https://docs.docker.com/guides/walkthroughs/images/getting-started-delete-stack.webp?border=true)

After you delete the application stack, follow the steps from [[#Run Docker Hub images]] to run the application again. Note that when you delete the containers and run them again, Docker Desktop persists any todos that you created.
### Summary

In this walkthrough, you persisted data between containers using a volume. You can use this to persist and share data among isolated and ephemeral containers.

## Access a local folder from a container

This walkthrough shows you how to access a local folder from a container. To better understand some concepts in this walkthrough, complete the [[#Run multi-container applications]] walkthrough first.

Docker isolates all content, code, and data in a container from your local filesystem. By default, containers can't access directories in your local filesystem.

![Data isolation diagram](https://docs.docker.com/guides/walkthroughs/images/getting-started-isolation.webp?w=400)

Sometimes, you may want to access a directory from your local filesystem. To do this, you can use bind mounts.

### Step 1: Get the sample application

If you have git, you can clone the repository for the sample application. Otherwise, you can download the sample application. Choose one of the following options.

Use the following command in a terminal to clone the sample application repository.

```console
$ git clone https://github.com/docker/bindmount-apps
```

### Step 2: Add a bind mount using Compose

Add a bind mount to access data on your system from a container. A bind mount lets you share a directory from your host's filesystem into the container.

![Bind mount diagram](https://docs.docker.com/guides/walkthroughs/images/getting-started-bindmount.webp?w=400)

To add a bind mount to this project, open the `compose.yaml` file in a code or text editor, and then uncomment the following lines.

```yaml
todo-app:
    # ...
    volumes:
      - ./app:/usr/src/app
      - /usr/src/app/node_modules
```

The `volumes` element tells Compose to mount the local folder `./app` to `/usr/src/app` in the container for the `todo-app` service. This particular bind mount overwrites the static contents of the `/usr/src/app` directory in the container and creates what is known as a development container. The second instruction, `/usr/src/app/node_modules`, prevents the bind mount from overwriting the container's `node_modules` directory to preserve the packages installed in the container.

### Step 3: Run the application

In a terminal, run the following commands to bring up your application. Replace `/path/to/bindmount-apps/` with the path to your application's directory.

```console
$ cd /path/to/bindmount-apps/
```

```console
$ docker compose up -d
```

### Step 4: Develop the application

Now, you can take advantage of the container’s environment while you develop the application on your local system. Any changes you make to the application on your local system are reflected in the container. In your local directory, open `app/views/todos.ejs` in a code or text editor, update the `Enter your task` string, and save the file. Visit or refresh [localhost:3001](http://localhost:3001/)⁠ to view the changes.

### Summary

In this walkthrough, you added a bind mount to access a local folder from a container. You can use this to develop faster without having to rebuild your container when updating your code.

## Containerize your application

When working with containers, you typically need to create a `Dockerfile` to define your image and a `compose.yaml` file to define how to run it.

To help you create these files, Docker Desktop has the `docker init` command. Run this command in a terminal within your project folder. `docker init` creates all the required files to containerize your application. This walkthrough shows you how this works.

### Step 1: Run the command to create Docker assets

Choose one of your own applications that you would like to containerize, and in a terminal, run the following commands. Replace `/path/to/your/project/` with the directory containing your project.

```console
$ cd /path/to/your/project/
```

```console
$ docker init
```

### Step 2: Follow the on-screen prompts

`docker init` walks you through a few questions to configure your project with sensible defaults. Specify your answers and press `Enter`.

### Step 3: Try to run your application

Once you have answered all the questions, run the following commands in a terminal to run your project. Replace `/path/to/your/project/` with the directory containing your project.

```console
$ cd /path/to/your/project/
```

```console
$ docker compose up
```

### Step 4: Update the Docker assets

The `docker init` command tries its best to do the heavy lifting for you, but sometimes there's some assembly required. In this case, you can refer to the [Dockerfile reference⁠](https://docs.docker.com/reference/dockerfile/) and [Compose file reference](https://docs.docker.com/compose/compose-file/)⁠ to learn how to update the files created by `docker init`.

### Summary

In this walkthrough, you learned how to containerize your own application.

## Publish your image

Follow this walkthrough to learn how to publish and share your images on Docker Hub.

### Step 1: Get the example image

To get the example image:

1. In Docker Desktop, select the search bar.
2. In the search bar, specify `docker/welcome-to-docker`.
3. Select **Pull** to pull the image from Docker Hub to your computer.

![Search Docker Desktop for the welcome-to-docker image](https://docs.docker.com/guides/walkthroughs/images/getting-started-search.webp?w=650&border=true)

### Step 2: Sign in to Docker

Select **Sign in** on the top-right of Docker Desktop to either sign in or create a new Docker account.

![Signing in to Docker Desktop](https://docs.docker.com/guides/walkthroughs/images/getting-started-signin.webp?w=300&border=true)

### Step 3: Rename your image

Before you can publish your image, you need to rename it so that Docker Hub knows that the image is yours. In a terminal, run the following command to rename your image. Replace `YOUR-USERNAME` with your Docker ID.

```console
$ docker tag docker/welcome-to-docker YOUR-USERNAME/welcome-to-docker
```

### Step 4: Push your image to Docker Hub

To push your image to Docker Hub:

1. In Docker Desktop, go to the **Images** tab
2. In the **Actions** column for your image, select the **Show image actions** icon.
3. Select **Push to Hub**.

![Pushing an image to Docker Hub](https://docs.docker.com/guides/walkthroughs/images/getting-started-push.webp?border=true)

Go to [Docker Hub](https://hub.docker.com/)⁠ and verify that the list of your repositories now contains `YOUR-USERNAME/welcome-to-docker`.

### Summary

In this walkthrough, you pushed and shared an image on Docker Hub.