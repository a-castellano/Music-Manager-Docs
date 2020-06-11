# Documentation about Music Collection Manager Project

## Purpose

The aim of this project is to help me in order to manage my music collection.

Also, I want to split this project in different microsrvices in order to improve my knowledge about microservices and Kubernetes. 

## Services

### Music Information Service

Repo

Main entry of this Service.

### Metal Archives Wrapper

[Repo](https://git.windmaker.net/musicmanager/metal-archives-wrapper)

This service retrieves Artitst and Records from [Metal Archives](https://www.metal-archives.com/)
If there is no info, this service publish a new job for Musicbrainz Wrapper.

### Musicbrainz Wrapper

Repo

This service retrieves Artitst and Records from [MusicBrainz](https://musicbrainz.org/)


## Service Intercomunication

The following graph shows how microservices are communicated.

```mermaid
graph TD;
User -- Asks for an artist, album or pendent job --> Music_Manager[Music Manager];
Music_Manager -- Returns requested info or pending job ID --> User;
Music_Manager -- Asks for requested info of job id --> Storage_Manager[Storage Manager];
Storage_Manager -- Check For Requested Info --> MusicInfoStore[(Music Info Store)];
Storage_Manager -- If info not found, check pending jobs --> MusicInfoStore[(Music Info Store)];

Storage_Manager-- Returns Info If Found --> Music_Manager;
Music_Manager -- No info found, create a job --> Job_Manager[Job Manager];
Job_Manager -- Store pendent job ID --> Storage_Manager;
Storage_Manager -- Store job id with pending status --> JobStatusStore[(Job Status Store)];

Job_Manager -- Look for info --> Metal_Archives_Wrapper[Metal archives wrapper];
Metal_Archives_Wrapper -- Retrieve info --> Metal_Archives((www.metal-archives.com));
Metal_Archives_Wrapper -- Returns retrieved info if found --> Job_Manager;
Job_Manager -- Not found at metal archives - Look for info --> Music_Brainz_Wrapper[Music Brainz wrapper];
Music_Brainz_Wrapper -- Retrieve info --> Music_Brainz((musicbrainz.org));
Music_Brainz_Wrapper -- Returns retrieved info if found --> Job_Manager;
Music_Manager -- If artist requested, send jobs for all albums --> Job_Manager;
```
