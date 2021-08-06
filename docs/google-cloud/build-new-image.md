### How to create an image
**Author: Aashna, Gali**
**Last modified: 2021-Aug-06 Jen Altreuter, Aashna Jhaveri**

The image stores the environments used to run the pipeline. Therefore, every time you make changes in the instance, you may want to store the current version of instance as a new image. Make sure the current instance works by doing a test run before generating a new Image.

1. STOP the instance (STOP, do not *delete*)

2. Go to Storage -> Disks.

3. Select the root disk that you want to make an image, e.g "chips-new-test" which is 20GB--click on it an it should bring you to the disk page.

4. On the disk page, select "Create Image" from the top
Here are the fields you will have to fill:
    - name: name of the image, i.e. chips-ver1-7c
    - source: (do not change it) it should say "disk"
    - source disk: (do not change it)
    - location: select regional, and then select us-east1 (south carolina)
    - family: chips
    - description: [add a brief description of what you have added/changed]
    - click add label, then add two labels:
        - key: group, value: plumbers
        - key: pipeline, value: chips
    ***The value can be 'chips','rima'or 'wes' depending on the 'pipeline' that you are creating/updating.***
    - then click "Create" (at the bottom)
