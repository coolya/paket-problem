This repo contains minimal reproduction code for paket restore problems with runtime identifiers

## Steps to reproduce

Easiest way to reproduce the problem is in a docker container to ensure that there are no nuget caches anywhere.

To reproduce run something like this:

````
docker run --rm -i -t -v $(pwd):/code  microsoft/dotnet

apt-get update &&  apt-get install -y apt-transport-https \
  && echo "deb http://download.mono-project.com/repo/debian jessie/snapshots/5.4.1.6 main" > /etc/apt/sources.list.d/mono-official.list \
  && curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > /etc/apt/trusted.gpg.d/microsoft.gpg \
  && apt-get update \
  && apt-get install -y --allow-unauthenticated mono-devel

cd /code

dotnet build
```

It will fail.

Now comment line 12 of the fsproj file and run it again. It should be successfull. Comment the line back in and re-run `dotnet build` and it will be successful.

### Workaround

Adding the packages for arm:

```
runtime.linux-arm.Microsoft.NETCore.App
runtime.linux-arm.Microsoft.NETCore.DotNetHostPolicy
runtime.linux-arm.Microsoft.NETCore.DotNetHostResolver
runtime.linux-arm.Microsoft.NETCore.DotNetAppHost
```

and linux

```
runtime.linux-x64.Microsoft.NETCore.App
runtime.linux-x64.Microsoft.NETCore.DotNetHostPolicy
runtime.linux-x64.Microsoft.NETCore.DotNetHostResolver
runtime.linux-x64.Microsoft.NETCore.DotNetAppHost
````

fixes the problem.
