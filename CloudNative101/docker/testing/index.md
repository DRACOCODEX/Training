# Simple Web Server

We installed docker and docker-compose, now to check that everything works as expected, we can try to build an image and run a container as we did in the last step or use the [official httpd](https://hub.docker.com/_/httpd) one. Since the httpd image does not exist locally it will be automatically fetched and built. Finally, a container based on it will be launched in the foreground (it will be automatically removed when stopped). We should be able to see the It works! message when we reach our machine ip via browser.

The Apache HTTP Server, colloquially called Apache, is a Web server application notable for playing a key role in the initial growth of the World Wide Web. Originally based on the NCSA HTTPd server, development of Apache began in early 1995 after work on the NCSA code stalled. Apache quickly overtook NCSA HTTPd as the dominant HTTP server, and has remained the most popular HTTP server in use since April 1996.

All we have to do is to run the following command:
```shell
sudo docker run --rm --name=linuxconfig-test -p 80:80 httpd
```
- rm (clean up flag)
- name (container name)
- p (port 80 VM to 80 Container)

## Verify in Browser

Open up a browser and visit http://localhost  

The result should be as shown below:

![Example Output](images/ItWorks.png)
