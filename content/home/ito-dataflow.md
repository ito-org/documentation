---
weight: 2
title: Dataflow on ito-App
---

# ito-App dataflow

| Acronym | Description |
| ------ | ------ |
| TCN | Temporary Contact Number |
| hash | A hashing function |
| Device | Smartdevice like smartphone |

The TCN on your Device will rotate every 30 Minutes and stored on your Phone with the Timestemp.

![TCN Sharing](images/home/Austausch_TCN.png)

Your phone will publish this TCN to every contact in your surrounding and collect their TCN.
The TCN will hashed before it will published by bluetooth so your Contacts don't know the real TCN.

![Device storage](images/home/data_storage_device.png)

You have two different storages on your phone, one for your private TCN and one for the collected TCN.
In the storage of your own TCN there will be stored the TCN and the published Day (timestamp in the image).
The collected TCN will be stored in another storage with the information of duration and distance and the Day you have collect that TCN.
Each storage will delete old informations. Old informations are currently informations older than 3 weeks.

If you got infected your health authority gives you a authorization code (Key) to upload a report.  

![Authorization](images/home/authorization.png)

Your phone will send the key to the TCN Server and ask for the permission to upload private TCN.
The TCN server ask the authorization server if this Key is valid.
If the key valid the TCN server fetch the report from your phone, if it is not valid he will drop the information.

![Report infected TCN](images/home/report.png)

Your Report contains the TCN from a defined time range. This time range is given by the health authority.
Your devices show you which data will be published to the server and you need to confirm.

The published TCN will be packed to a bundle to download for the user.
In this bundle are TCN from more people.
This download bundle will be created each hour and fill up to 200 TCN so nobody can collect a small amount of TCN and deanonymize people.

![Download infected TCN](images/home/download.png)

Each time you want to look if you where in contact with an infacted people, your device download the new TCN from the Server.
For that it ask the Server for the bundles after the last downloadet bundle.
