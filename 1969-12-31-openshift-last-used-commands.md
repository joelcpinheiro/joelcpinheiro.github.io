---
title: OpenShift last used commands
slug: openshift-last-used-commands
date_published: 1970-01-01T00:00:00.000Z
date_updated: 2022-07-05T13:58:22.000Z
tags: #Import 2023-04-07 15:04
draft: true
---

These are my pretty commands that I use every day to help my work...

Delete pods by with a specific status(ex: ImagePullBackOff):

oc get pods | grep -i imagepullbackoff| awk '{print $1}' | xargs oc delete pod
