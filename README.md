# Custom Spotify Playlist Using Terraform!

Build A Spotify Playlist using Docker and Terraform!


![bart](/assets/bart.gif)

![diagram](/assets/diagram.png)

## Table of Contents
- [Prerequisites](#prerequisites)
- [Walkthrough](#walkthrough)
- [Result](#result)
- [Challenges](#challenges)

## Prerequisites

- [Terraform](https://developer.hashicorp.com/terraform/install)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [Spotify](https://open.spotify.com)
- [Spotify Developer](https://developer.spotify.com)
- [Vscode](https://code.visualstudio.com)

## Walkthrough
I posess a Spotify account so all I needed was to create a Spotify Developer account. A free spotify account will suffice.

First we need to setup the Spotify provider in order to obtain and use the resources. We will use the spotify provider [here](https://registry.terraform.io/providers/conradludgate/spotify/latest) and pressing the use provider to display code or below. 

```bash
terraform {
  required_providers {
    spotify = {
      source = "conradludgate/spotify"
      version = "0.2.7"
    }
  }
}

provider "spotify" {
  # Configuration options
}
```


![Spotify Provider](/assets/spotifyprovider.jpg)



provider.tf file should look like this and we will  add the configuration options later: 
![ProviderTF](/assets/providertf.png)

Next we will need an API Key and this will be used to connect with the Spotify account. After creating a free Spotify account, Create a Developer account.

![createapp](/assets/spotifydev.jpg)

Enter the details to your liking except the Redirect URL must be:
```
http://localhost:27228/spotify_callback
```
 ![spoptifydetails](/assets/spotifyappdet.jpg)

Next we will need to grab the Client ID and Client Secret from the settings. We will store these two codes in an .env file.
![Settings](/assets/settings.jpg)
```
SPOTIFY_CLIENT_ID=<enter_spotify_client_id_without>
SPOTIFY_CLIENT_SECRET=<enter_spotify_client_secret>

```
Should look like this below:

![env](/assets/env.png)

Run docker to retrieve the API Key. Confirm Docker Desktop is running first.

```
docker run --rm -it -p 27228:27228 --env-file .env ghcr.io/conradludgate/spotify-auth-proxy

```
Authorization successful will display if setup correctly. Copy the API Key and enter in a new file called terraform.tfvars. Then press the link in Auth Url: and the page will open to allow access to your Spotify account. Leave terminal open and create a new terminal:

![authsuccess](/assets/dockerrun.png)

![tf](/assets/terraformapi.png)

Set a variable in the provider.tf file in configuration options:
![provup](/assets/provup.png)

Create the variables.tf file to hold the api_key variable as a string type:
```bash
variable "api_key" {
    type = string
}
```
![variable](/assets/variable.png)

The fun part is here. Now we will run terraform init which will run and install the files we have created.

![terraforminit](/assets/terraforminit.png)

Create Spotify playlist by first creating a playlist.tf file. The artist of choice is Larry June one of my favorite artists. More resources can be found on the documentation of the spotify provider [here](https://registry.terraform.io/providers/conradludgate/spotify/latest)

```bash
data "spotify_search_track" "by_artist" {
  artist = "Larry June"
}


resource "spotify_playlist" "Larry_June_Terraform" {
  name        = "Larry June Terraform"
  description = "This playlist was created by Terraform"
  public      = true

  tracks = [
    data.spotify_search_track.by_artist.tracks[0].id,
    data.spotify_search_track.by_artist.tracks[1].id,
    data.spotify_search_track.by_artist.tracks[2].id,
  ]
}

```

After the information has been entered in, next is terraform plan in terminal. This will tell us the actions that will be executed when applied. I created a previous playlist so notice how that will be destroyed when the code has been updated:
![plan](/assets/terraformplan.png)


terraform apply -auto-approve will apply the actions:
![apply](/assets/apply.png)




## Result

![lj](/assets/lj.png)


## Challenges

Docker issue: Docker for Windows: image operating system “linux” cannot be used on this platform.
- Check Docker Desktop Builders to ensure desktop-linux is up and running. if desktop-windows is running, the error will display. If error persists, try to uninstall and reinistall Docker. 

![dockbuild](/assets/dockbuild.png)

Code Error: Inappropriate value for attribute "artist": string required.
- Confirm brackets aren't wrapped around the artist name. Only quotations.
![code](/assets/codeerror.png)



