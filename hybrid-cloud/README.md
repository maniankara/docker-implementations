This explains how to implement hybrid cloud containers. Here is an example for selenium grid with AWS

1. Accure an elastic ip in AWS and request for a firewall opening for that ip to forward to host H inside your corporate premise
2. Install `docker` on host H 
3. Install selenium hub on the host machine H
`docker run -d -P --name selenium-hub selenium/hub`
4. This will open some port P on the host machine H which can be verified with `docker ps`
5. Run ambassador besides it on host H
`docker run -d --link selenium-hub:selenium-hub --name selenium_ambassador -p 32769:4444 svendowideit/ambassador`
6. Verify if the ambassador works with curl on host H itself `curl localhost:32769`
7. Create an AWS EC2 `t2.micro` instance https://docs.docker.com/engine/installation/cloud/cloud-ex-aws/ and assign the elastic ip to it
8. Telnet from that instance to host H (internet facing IP) over port P to verify that the port opening works.
9. Link the ambassador of host H from AWS EC2 `docker run -d --name selenium_ambassador --expose 32769 -e SELENIUM_PORT_32769_TCP=tcp://<PUBLIC_IP>:32769 svendowideit/ambassador`
10. Create a proxy selenium-hub for the ambassador `docker run -d -p 4444:32769 svendowideit/ambassador 172.17.0.2 32769`
11. Now the selenium hub should be accessible from AWS EC2 itself, verify it `curl localhost:4444`
