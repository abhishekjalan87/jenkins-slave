FROM  jenkins/inbound-agent:latest-jdk11 

LABEL Abhishek Jalan <abhishekjalan1987@gmail.com> 

USER root

ENV PYTHON_VERSION 3.8.6

RUN apt-get update && \
    apt-get -y install apt-transport-https \
    ca-certificates \
    curl \
    gnupg2 \
    software-properties-common && \
    curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg > /tmp/dkey; apt-key add /tmp/dkey && \
    add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") \
    $(lsb_release -cs) \
    stable" && \
    apt-get update && \
    apt-get -y install docker-ce 
RUN usermod -a -G docker jenkins && \
    sudo sh -c "echo \"jenkins ALL=(ALL) NOPASSWD: ALL\" >> /etc/sudoers"


#Install Python3.8.6
RUN apt-get install -y openssl make build-essential libssl-dev zlib1g-dev libbz2-dev \
    libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
    xz-utils tk-dev libffi-dev liblzma-dev libgdal-dev python3-dev python3-pip zip gettext-base
RUN wget --force-html "https://www.python.org/ftp/python/${PYTHON_VERSION}/Python-${PYTHON_VERSION}.tgz" && \
    tar -xvf Python-${PYTHON_VERSION}.tgz && \
    cd Python-${PYTHON_VERSION} && ./configure && sudo apt-get install zlib1g-dev && sudo make -j 4 && sudo make install
RUN /usr/local/bin/python3.8 -m pip install --upgrade pip
RUN rm -f Python-${PYTHON_VERSION}.tgz && \
    unlink /usr/bin/python && \
    ln -s /usr/local/bin/python3.8 /usr/bin/python && \
    ln -s /usr/local/bin/pip3 /usr/bin/pip

RUN pip install ansible 
RUN mkdir -p /home/jenkins/.ansible && \
    mkdir -p /home/jenkins/.ssh && \
    chown -R 1000:1000 /home/jenkins/.ansible && \
    chown -R 1000:1000 /home/jenkins/.ssh

##Install Docker Compose
RUN sudo curl -L "https://github.com/docker/compose/releases/download/1.26.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
RUN sudo chmod +x /usr/local/bin/docker-compose

##Install AWS CLI 2.1.6
RUN apt-get install -y unzip && \ 
    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" && \
    unzip awscliv2.zip && \
    ./aws/install && \
    rm -f awscliv2.zip && \
    aws --version

USER jenkins