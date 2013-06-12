HOWTO manage PyWPS Tutorial
===========================

NOTE: before you start, you need at least pywps/trunk and pywps/docs
directories side-by-side in order to build this documentation

step #1: edit source/*


step #2: run

    make html


step #3: rsync

    rsync --protocol 29 --delete-excluded --del -r build/html/ $DEVELOPER@wald.intevation.org:/pywps/htdocs/documentation/course
