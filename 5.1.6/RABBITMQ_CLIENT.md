# RabbitMQ Client Install

## Download Client
1. Download the v0.14.0 RabbitMQ Client [here](https://github.com/alanxz/rabbitmq-c/archive/refs/tags/v0.14.0.zip)

##### On Server
```bash
wget https://github.com/alanxz/rabbitmq-c/archive/refs/tags/v0.14.0.zip
```

OR

##### Download to your computer and upload via SCP
1. [Download from here](https://github.com/alanxz/rabbitmq-c/archive/refs/tags/v0.14.0.zip)

2. If you are on Windows, use Git Bash and run the following command in the directory where you downloaded this file:
```bash
scp v0.14.0.zip username@server.name:~
```

2. Copy the zip file to /opt and extract
```bash
sudo cp v0.14.0.zip /opt
```
```bash
cd /opt
```
```bash
sudo unzip v0.14.0.zip
```
```bash
sudo rm v0.14.0.zip
```

3. In the client directly extracted, create a build directory
```bash
cd rabbitmq-c-0.14.0
```
```bash
sudo mkdir build && cd build
```

4. Run the following CMake commands in the build directory:
```bash
sudo cmake ..
```
```bash
sudo cmake --build .
```
5. Run the ls command to see if an include folder is present after the build command:
```bash
ls
```

6. If the include folder is in the build folder then copy the contents in that folder to the following location:
```bash
sudo cp ./include/rabbitmq-c/* ../include/rabbitmq-c/
```

7. Create/Modify link to the /usr/local/include/rabbitmq
```bash
sudo ln -s /opt/rabbitmq-c-0.14.0/include /usr/local/include/rabbitmq
```

8. Create symbolic link to lib
```bash
sudo ln -s $PWD/librabbitmq/librabbitmq.so.4 /usr/local/lib/librabbitmq.so.4
```

9. Create a symbolic link for librabbitmq.a
```bash
sudo ln -s $PWD/librabbitmq/librabbitmq.a /usr/local/lib/librabbitmq.a
```

[Next Install Apache FOP](APACHEFOP.md)