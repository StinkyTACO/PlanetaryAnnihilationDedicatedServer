# PlanetaryAnnihilationDedicated
Dedicated Server Setup for Planetary Annihilation

I wanted to use docker, as all the images were depreciated by now. However there are challenges to making this 100% automated and available on the hub.
Namely, steamcmd will not work anonymously. You must login and persist that login. And i made some modifications to an existing docker container to allow more flexibility in the startup parameters of the server.

# What is Planetary Annihilation?
Planetary Annihilation Titans is a standalone expansion of the original Planetary Annihilation, a real-time strategy game. It was released on August 18, 2015, and adds 21 new units to the game, including five Titan-class units. It also adds multi-level terrain, a bounty mode, and an improved tutorial1. The game allows players to command armies with numbers in the thousands across multiple planets on land, sea, air, and even in orbit. It also features epic multiplayer, where up to ten friends can play in massive free-for-all and team-based matches.

This Docker image contains the dedicated server of the game.

>  [PA Titans](https://store.steampowered.com/app/386070/Planetary_Annihilation_TITANS/)

# How to use this image
## Getting Started.

**Initial one-time setup**

As of now, you can't download the PA Titans dedicated server using `+login anonymous`.

1. [Create a fresh Steam account](https://store.steampowered.com/join/) and add PA to its library or use your own. [Optional if you already have an account]<br/> 

2. Create required named volume:
```console
$ docker volume create steamcmd_login_volume # Location of login session
```
Note if you use docker-compose - like I do, you need to mount the volume as an external volume.

See the repo for an example compose file.

```console
$ docker volume create docker_steamcmd_login_volume # Location of login session
```

3. Activate the SteamCMD login session, if required enter your e-mail Steam Guard code (this will permanently save your login session in `steamcmd_login_volume`). Replace the following fields before executing the command:
- [STEAMUSER] - steam username
- [ACCOUNTPASSWORD] - steam account password
```console
$ docker run -it --rm \
    -v "steamcmd_login_volume:/home/steam/Steam" \
    ghcr.io/obiwantoby/pa-dedicated-server:latest \
    bash /home/steam/steamcmd/steamcmd.sh +login [STEAMUSER] [ACCOUNTPASSWORD] +quit
```

**Running a PA Titans dedicated server**

4. Run using a bind mount for data persistence on container recreation. Replace the following fields before executing the command:
- [STEAMUSER] - steam username (no password required, if you completed step 1)
```console
$ mkdir -p $(pwd)/pa-data
$ chmod 777 $(pwd)/pa-data # Makes sure the directory is writeable by the unprivileged container user
$ docker run -d --net=host \
    -v $(pwd)/pa-data:/home/steam/PlanetaryAnnihilation-dedicated/ \
    -v "steamcmd_login_volume:/home/steam/Steam" \
    --name=pa-dedicated -e STEAMUSER=[STEAMUSER] ghcr.io/obiwantoby/pa-dedicated-server:latest
```
Note you will want to make sure you set appropriate permissions for the folder. While 777 is the easy way - I suggest a chmod go+rwx and passing in the GID and PID of the user you wish to access the folder. Alternatively the container can install the binaries and everyone would be non the wiser.
# Credits

This repository is based on [https://github.com/CM2Walki/CS2/](https://github.com/CM2Walki/CS2/) .<br/>
This repository is inspired by [https://github.com/XanderXAJ/docker-planetary-annihilation-server/ ](https://github.com/XanderXAJ/docker-planetary-annihilation-server) .<br/>
This container is based on [https://github.com/obiwantoby/PlanetaryAnnihilationDedicated](https://github.com/obiwantoby/PlanetaryAnnihilationDedicated)

**The container will automatically update the game on startup, so if there is a game update just restart the container.**
