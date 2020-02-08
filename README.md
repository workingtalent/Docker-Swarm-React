# Docker Swarm React

### Step-by-step
1. Make sure [NodeJS](https://github.com/nodesource/distributions/blob/master/README.md#debinstall) and [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/) are installed on your development machine (either in Azure or on your own laptop).
2. create a new react app by running `npx create-react-app react-app` follow instructions on your screen.
3. Start the App and browse to http://localhost:3000/
4. Write / modify the `Dockerfile`
5. Containerize the React App `docker build -t react-app:development .`
6. Run the container and mount/bind the `react-app` direcrory inside the container to the underlaying host `docker run -it -v ${PWD}:/react-app -v /react-app/node_modules -p 8080:3000 --rm react-app:development`
This allows you to change code inside the container without building the container over and over again. Perfect for development.

    **Note:** make sure you stop de running React app from step 2 first. You can not run 2 apps on the same port.
7. Open the running container in the browser http://localhost:3000/

8. Prepare Dockerfile for production (See `Dockerfile-prod`)
9. `docker build -f Dockerfile-prod -t react-app:prod .`
10. Test if it still works `docker run -it -p 80:80 --rm react-app:prod` http://localhost
11. Publish docker image to Docker hub.

    11.1 `docker tag react-app:prod dockerusername/react-app:prod`

    11.2 `docker push dockerusername/react-app:prod`

12. Make sure you have 3 VM's with [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/) installed.
13. Login into the first server and initialise a docker swarm.
14. Use the docker swarm join --token <workerToken> to add the other 2 servers to your swarm.
15. Make sure the following ports are open on all servers: 2376 tcp, 2377 tcp, 7946 udp/tcp, 4789 udp

    **Note:** This is NOT secure since all servers on the internet can be added to your swarm. For demo this is fine, your servers will be deleted daily. But dont do this in production!
16. Create a docker service `docker service create --name react-app dockerusername/react-app:prod`
17. Scale up the docker service `docker service scale react-app=3`

## BONUS

Now we have gone through the steps to containerize a React/NodeJS app and run it inside a Docker swarm for high availability and ease of scaling the Application up and down.

Next steps: Prepare a CI/CD pipeline with Github Actions (which is similar to Azure DevOps Yaml Pipelines) to automate the entire build and deployment of the React app.