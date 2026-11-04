# SYNO.Foto.Browse.Album
## method: list
Authlevel: 1<br/>
This means, you have to be authenticated first.
### Description:
List all albums.
### Request:

| Parameter | Description | Value | Default Value |
| --- | --- | --- | --- |
| offset | Required. Specify how many shared folders are skipped before beginning to return listed shared folders. | Integer | 0 |
| limit | Required. Number of shared folders requested. 0 lists all shared folders. | Integer | 0 |
| category | Which albums to list. `"normal"` lists normal albums. | String | |
| sort_by | Optional. Field to sort on. The Photos web client sends `"album_name"`. | String | |
| sort_direction | Optional. Specify to sort ascending or to sort descending. <br/>Options include: <br/><b>asc:</b> sort ascending. <br/><b>desc:</b> sort descending. | asc or desc | asc |
| additional | Optional. `["sharing_info","thumbnail"]` adds the sharing state and the album cover. `thumbnail` has the same shape as for items (`unit_id`, `cache_key`, `sm`, `m`, `xl`). | JSON array of strings | |

### Example:
```
https://<IP_ADDRESS>/photo/webapi/entry.cgi?api=SYNO.Foto.Browse.Album&version=4&method=list&offset=0&limit=100&category="normal"&sort_by="album_name"&sort_direction="asc"&additional=["sharing_info","thumbnail"]
```
String values are JSON-encoded, including the quotes. This is the request the Photos 1.9.1 web client sends.

Sharing an album does not change its `id`, `type` (`normal`) or owner, and it stays in this list; only `shared` changes.

To list the items of an album, use `SYNO.Foto.Browse.Item` `list` with `album_id=<id>`.

### Response:
`<data>` object definitions:

| Parameter | Type | Description |
| --- | --- | --- |
| cant_migrate_condition | `cant_migrate_condition` object |   |
| condition | `<condition>` object  | Arguments for conditional album |
| create_time | Linux timestamp in second |   |
| end_time | Linux timestamp in second |   |
| freeze_album |   |   |
| id | Integer | id of album |
| item_count | Integer | Total number items in album. |
| owner_user_id | Integer | UID of album owner. |
| passphrase |  |  |
| shared |  |   |
| sort_by |   |   |
| sort_direction |   |   |
| start_time |   |   |
| temporary_shared |   |   |
| type |   |   |
| version |   |   |

`<condition>` object definition:

| Parameter | Type | Description |
| --- | --- | --- |
| folder_filter | Integer |   |
| user_id | Integer |   |

### Example:
```YAML
{
   "data":{
      "list":[
         {
            "cant_migrate_condition":{
               
            },
            "condition":{
               
            },
            "create_time":1633947628,
            "end_time":1631815585,
            "freeze_album":false,
            "id":8,
            "item_count":3,
            "name":"My Album Name",
            "owner_user_id":2,
            "passphrase":"",
            "shared":false,
            "sort_by":"default",
            "sort_direction":"default",
            "start_time":1631387203,
            "temporary_shared":false,
            "type":"normal",
            "version":442042
         },
         {
            "condition":{
               "folder_filter":[
                  1423
               ],
               "user_id":0
            },
            "create_time":1633865347,
            "id":7,
            "item_count":257,
            "name":"My Conditional Album Name",
            "owner_user_id":2,
            "passphrase":"",
            "shared":false,
            "sort_by":"default",
            "sort_direction":"default",
            "type":"condition",
            "version":602595
         }
      ]
   },
   "success":true
}
```
