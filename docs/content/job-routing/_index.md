---
title: Job Routing
geekdocFlatSection: true
---
Any info Retrieval request sended to Jobs manager is routed by **Job Router**.

Job routig depends on Job's **LastOrigin** property which contains the name of the last services that proccessed the [job](/common-types/#job).

For each job received by **Job Router**, this service routes it depending of **LastOrigin** and **Finished** properties. **LastOrigin** will have the following values for the time being:

* JobManager
* MetalArchivesWrapper
* MusicBrainzWrapper

The value of **LastOrigin** is changed by the service who process it.

Job Routing behaviour:

{{<mermaid align="left">}}
graph TD;
Job_Manager -- Send new job --> Job_Router[Job Router];
Job_Router -- Route job --> Metal_Archives_Wrapper[Metal archives wrapper];
Metal_Archives_Wrapper -- Returns retrieved info or failed state--> Job_Router;
Job_Router -- Not found at metal archives - Look for info --> Music_Brainz_Wrapper[Music Brainz wrapper];
Music_Brainz_Wrapper -- Returns retrieved info or failed state--> Job_Router;
{{< /mermaid >}}

If **RequiredOrigin** has a value, **Job Router** will send the job only to required Origin and it won't be re-routed.

Finnaly, **Job Router** will send final to **Status Manager** and required information data to **Storage Manager** if exists.
