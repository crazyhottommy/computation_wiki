# How to build reference snapshot
**Author: Gali Bai, Aashna Jhaveri**
**Last Modified: 2021-Aug-06 Aashna Jhavery and Jen Altreuter**


This tutorial can be used when references need to be stored or updated for the CIDC pipelines in [snapshot](https://cloud.google.com/compute/docs/disks/create-snapshots)] on GCP.
After the references are created or updated on GCP console for particular pipeline, stop the instance and follow the steps mentioned below can be used to create the reference snapshot.
Here, we have described the image particular to CHIPS as an example. Please refer to IMAGES under GCP to find the latest image for each pipeline.


```

1. Log out the instance: hit CTRL-D and stop the instance (do not *delete*).

2. Go to `Storage` -> `Disks`.

2. Select the boot disk on which you stored the reference file.

4. On the disk page, select `Create Snapshot` from the top
Here are the fields you will have to fill:
    - name: name of the image, i.e. chips-ref-ver1-0
    - Description: briefly summarize the updates of current reference version
    - source: (do not change it) it should say "disk"
    - source disk: (do not change it)
    - location: select regional, and then select us-east1 (South Carolina)
    - description: [add a brief description of what you have added/changed]
    - click add label, then add two labels:
        - `key: group`, `value: plumbers`
        - `key: pipeline`, `value: chips`  
    ***The value can be 'chips','rima'or 'wes' depending on the 'pipeline' that you are creating/updating.***
    - then click "Create" (at the bottom)
