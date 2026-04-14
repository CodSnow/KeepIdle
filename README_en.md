# KeepIdle

**English** | [**简体中文**](README.md)

## Usage

Download executable file from Release. 
Repository: [https://github.com/CodSnow/KeepIdle](https://github.com/CodSnow/KeepIdle)

Start a screen on the server and run it.
If you want to learn about screen command, just Google.

Command arguments：

```shell
./KeepIdle -cp 0.15 -m 2 -n 4h
```

In which:

-c enables CPU periodic waste, followed by the interval between wastes.  
E.g. waste CPU every 12 hours 23 minutes and 34 seconds, then the argument would be `-c 12h23m34s`.
Just follow this template.

-cp enables coarse-grained CPU percentage waste, and the waste rate will change in real time with the usage level of the machine.  
If the maximum waste of 20% of the CPU is `-cp 0.2`. The value range of percentage is [0, 1] and be careful not to use it with `-c`.

-m enables memory waste, followed by a number in GiB.  
After startup, the specified amount of memory will be occupied and will not be released until the process is killed.

-mp enables percentage-based dynamic memory waste.  
E.g., `-mp 0.2` will automatically adjust the memory footprint to keep 20% memory usage and prevent system OOM. Do not use this together with `-m`.

-n enables network(bandwidth) periodic waste, followed by the interval between wastes.  
Argument format is the same as `-c`. The Ookla Speed Test will be performed periodically (and the results will be output!)

-t specifics the number of concurrent connections of the network periodic waste.  
The default is 10. The larger the value, the more resources will be consumed. For most situations, it does not need to be changed.

-night-start and -night-end specify the night period (0-23 hours) for network speedtest waste.  
E.g., `-night-start 22 -night-end 6` means testing is only allowed between 22:00 and 06:00 of the next day.

-idle sets the network idle connection threshold (default 5).  
Combined with the night period parameters, the network speedtest waste will only execute when the system's 'established' connections are below this threshold and it is during the night time, to minimize interference with normal services.

-p specifics the process priority, followed by a priority value. If not specified, the lowest priority of the platform will be used by default.  
For UNIX-like systems (such as Linux, FreeBSD, and macOS), the value range is [-20,19], and the higher the number, the lower the priority.  
For Windows, see [the official documentation](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-setpriorityclass).  
It is recommended not to specify because the default is the lowest priority, making way for all other processes.

*All the functions you configured will be executed once immediately when you start the program, so you can take a look at the effect.*

## Docker Deployment

### Method 1: Docker Run

```bash
docker run -d \
  --name keepidle \
  --net=host \
  -e TZ=Asia/Shanghai \
  --restart unless-stopped \
  ghcr.io/codsnow/keepidle:latest \
  -cp 0.25 -mp 0.2 -n 4h -night-start 0 -night-end 6 -idle 5
```

### Method 2: Docker Compose (Recommended)

Create a `docker-compose.yml` file:

```yaml
services:
  keepidle:
    image: ghcr.io/codsnow/keepidle:latest
    container_name: keepidle
    restart: unless-stopped
    network_mode: host
    environment:
      - TZ=Asia/Shanghai
    command: ["-cp", "0.25", "-mp", "0.2", "-n", "4h", "-t", "10", "-night-start", "0", "-night-end", "6", "-idle", "5"]
```

Then run:

```bash
docker-compose up -d
```

---

## Credits
This project is based on [layou233/NeverIdle](https://github.com/layou233/NeverIdle). Thanks to the original author [layou233](https://github.com/layou233) for the excellent foundation!
