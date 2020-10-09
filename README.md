# Documentation about Music Collection Manager Project

[Actual Repo](https://git.windmaker.net/musicmanager/Music-Collection-Manager-Docs)

## Purpose

The aim of this project is to help me in order to manage my music collection.

Also, I want to split this project in different microsrvices in order to improve my knowledge about them and microsrvices architecture.

## Service Intercomunication

The following graph shows how microservices are communicated.

```mermaid
graph TD;
User -- Asks for an artist, album or pendent job --> Request_Manager[Request Manager];
Request_Manager -- Returns requested info or pending job ID --> User;
Request_Manager -- Request info if job has finished --> Storage_Wrapper[Storage Wrapper];
Request_Manager -- Asks for job satus --> Status_Wraper[Status Manager];
Storage_Wrapper -- Check for requested info --> MusicInfoStore[(Music Info Store)];

Storage_Wrapper[Storege Wrapper] -- Returns info --> Request_Manager;
Request_Manager -- No job found, create a job --> Job_Manager[Job Manager];
Job_Manager -- Store pendent job ID --> Status_Manager;
Status_Manager -- Store job ID with pending status --> JobStatusStore[(Job Status Store)];
Status_Manager -- Update job with finished status --> JobStatusStore[(Job Status Store)];
Status_Wraper[Status Wraper] -- Check job status --> JobStatusStore[(Job Status Store)];

Job_Manager -- Send new job --> Job_Router[Job Router];
Job_Router -- Route job --> Metal_Archives_Wrapper[Metal archives wrapper];
Metal_Archives_Wrapper -- Retrieve info --> Metal_Archives((metal-archives));
Metal_Archives_Wrapper -- Returns retrieved info--> Job_Router;
Job_Router -- Not found at metal archives - Look for info --> Music_Brainz_Wrapper[Music Brainz wrapper];
Music_Brainz_Wrapper -- Retrieve info --> Music_Brainz((musicbrainz));
Music_Brainz_Wrapper -- Returns retrieved info --> Job_Router;
Job_Router -- Job has finished  - Send retrieved info to be stored --> Storage_Manager[Storage Manager];
Job_Router -- Job has finished  - Send job final status to be stored --> Status_Manager[Status Manager];
Storage_Manager[Storage Manager] -- Store retrieved info --> MusicInfoStore[(Music Info Store)];
```

## Services

### Request Manager

[Repo](https://git.windmaker.net/musicmanager/Request-Manager)

Main entry of this Service, serves public API and communicates with other services.

Functions:
* Manage User requests
* Retrieve Required info.
* If required info was not found, a job with info retrieval request is requested to **Job Manager**.

### Job Manager

[Repo](https://git.windmaker.net/musicmanager/Job-Manager)

This service manages job traffic.

Functions:
* Creates jobs for retrieving artists and records info, which are received by **Job Router**.
* Creates jobs status.

### Job Router

[Repo](https://git.windmaker.net/musicmanager/Job-Router)

* Routes jobs to wrappers.
* If wrappers does not find required info, job is routed to the next configured wrapper.
* When there are no more wrappers to route jobs, this service sends info to **Storage Manager**.
* When there are no more wrappers to route jobs, this service send final job status to **Status Manager**.

### Metal Archives Wrapper

[Repo](https://git.windmaker.net/musicmanager/metal-archives-wrapper)

This service retrieves Artists and Records info from [Metal Archives](https://www.metal-archives.com/)

With or without info, state is returned to **Job Router**.

### Musicbrainz Wrapper

[Repo](https://git.windmaker.net/musicmanager/Musicbrainz-Wrapper)

This service retrieves Artists and Records from [MusicBrainz](https://musicbrainz.org/)

With or without info, state is returned to **Job Router**.

### Storage Manager

[Repo](https://git.windmaker.net/musicmanager/Storage-Manager)

This service stores and updates Artists, Albums and Jobs info.

### Storage Wrapper

[Repo](https://git.windmaker.net/musicmanager/storage-wrapper)

This service retrieves info from Music Info Storage.

### Status Manager

[Repo](https://git.windmaker.net/musicmanager/status-manager)

This service stores and updates jobs statuses.

### Status Wrapper

[Repo](https://git.windmaker.net/musicmanager/status-wrapper)

This service retireves jobs statuses.


## Common Libraries

Some microservices will use the same data types for their communications, instead of duplicating code, shared libraries will be used.

### Common types

[Repo](https://git.windmaker.net/musicmanager/Common-Types)

Common types used by Music Manager microservices ecosystem.
