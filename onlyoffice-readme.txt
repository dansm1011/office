
On the offline Ubuntu machine, after copying the files into a directory, verify them:
sha256sum -c SHA256SUMS


5. How your friend restores the environment
On the offline Ubuntu computer, your friend should first extract the sources:
tar -xzf onlyoffice-complete-sources.tar.gz


Then load the images:
sudo docker load -i docker-images/onlyoffice-offline-dev.tar.gz
sudo docker load -i docker-images/onlyoffice-documentserver.tar.gz


To test the development environment, assuming the development image uses the expected ONLYOFFICE layout:
cd onlyoffice-work

sudo docker run -it \
  --name onlyoffice-dev \
  -p 8080:80 \
  -v "$PWD/sdkjs:/var/www/onlyoffice/documentserver/sdkjs" \
  -v "$PWD/web-apps:/var/www/onlyoffice/documentserver/web-apps" \
  onlyoffice-offline-dev:local


This mounts the frontend source code into the container.

One important limitation: The command above does not mount your friend's server source code. The container may therefore use its bundled server implementation rather than the customized one. Your friend will need the matching server development workflow to build and test backend changes.

Also, the development container may try to download dependencies during startup. You should test its startup with internet access blocked before relying on it offline.
6. Final recommendation
For your friend's use case, the three main deliverables are:
Source archive

sdkjs + web-apps + server + supporting repositories



Development Docker image

Build tools and preinstalled runtime environment



Checksums and dependency verification

Ensures the transfer is intact and the environment has been tested



You're close to finishing. The last critical check is to verify that your development Docker image can start using the transferred source files without internet access, especially because your friend wants to modify server as well as sdkjs and web-apps.
