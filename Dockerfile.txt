#Pull base image
FROM ubuntu:14.04

# Dockerfile that uses mono 4.0.1 and Ubuntu
MAINTAINER Eric Irwin <eirwin@integrate.com>

# Run commands to modify the new image
RUN apt-get update && apt-get install -y \ 
    curl \
    nano 
RUN sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
RUN echo "deb http://download.mono-project.com/repo/debian wheezy snapshots 4.0.0" | sudo tee /etc/apt/sources.list.d/mono-xamarin.list
RUN sudo apt-get install -y mono-devel

# Build and Configure NuGet
RUN sudo apt-get install -y git
RUN sudo mozroots --sync --import
RUN sudo git clone https://github.com/Haacked/Nuget /tmp/nuget

# Hack around errors when running nuget build script
RUN /bin/bash -c 'cd /tmp/nuget && ./build.sh'

#CMD ['/tmp/nuget/build.sh']
RUN sudo cp /tmp/nuget/lib/NuGet.exe /usr/bin/NuGet.exe

# Add Integrate NuGet source
RUN  mono /usr/bin/NuGet.exe sources Add -Name Integrate -Source 'https://www.myget.org/F/integrate/auth/dda94e9b-6e72-41f0-8afa-d87526e5ec7f/'

#CMD ["alias nuget='mono /usr/bin/NuGet.exe'"]

# alias 'nuget' for easy executions
#CMD ["alias","nuget='mono /usr/bin/NuGet.exe'"]

# Cleanup Uneeded NuGet Resources
RUN sudo rm -r /tmp/nuget