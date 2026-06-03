iamge vs continer , image just has the dependencies listed in it like a txt file , but container has those dependenceis downloaded in them  

when container is loaded , it checks in local if the things are downloaded or not , if not it pulls them form docker hub 

to create image , we need some of the base images , like os ubantu etc 

in virtualization , the os on which we cerated the project had to be sent to other machine , so like if i was developed on windows , windows has to be packaged with the code and sent as guest os 

hypervisor acts as dummy hardware for guest os , guest os feels like it is geeting real hardware in virtualization , but the hypervisor like virtualbox communicated with host os to do hardware operations , or else it directly access hardware , but guest os does not access harware directly 

Dockerfile
Script/recipe that tells Docker how to build the image.
Steps: pick base image, install dependencies, copy files, set workdir, define CMD.
Image = snapshot with everything baked in.
Container = running instance of that image.
Docker Hub = registry of images (like GitHub but for Docker).