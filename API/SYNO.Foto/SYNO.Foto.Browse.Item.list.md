# SYNO.Foto.Browse.Item
## method: list
Authlevel: 1<br/>
This means, you have to be authenticated first.
### Description:
List all items in all folders in `Personal Space`.
### Request:

| Parameter | Description | Value | Default Value |
| --- | --- | --- | --- |
| offset | Required. Specify how many shared folders are skipped before beginning to return listed shared folders. | Integer | 0 |
| limit | Required. Number of shared folders requested. 0 lists all shared folders. | Integer | 0 |
| folder_id | Optional. ID of folder | Integer | |
| sort_by | Optional.<br/>Only working, when `folder_id` is given.<br/>Specify which file information to sort on. <br/>Options:<br/>**filename**: file name<br/>**filesize**: file size<br/>**takentime**: time of taking image<br/>**item_type**: type of the item | filename, filesize, takentime, item_type | filename |
| sort_direction | Optional.<br/>Only working, when `folder_id` is given.<br/>Specify to sort ascending or to sort descending.<br/>Options include: <br/>**asc**: sort ascending. <br/>**desc**: sort descending. | asc or desc | desc |
| type | Type of data<br/>**photo**: Photo<br/>**video**: Video<br/>**live**: iPhone live photos | String |
| passphrase | Passphrase for a shared album, i.e. `xXHXZxIov`. Only items of this album will be listed. | String | |
| additional | Get additional information about an item.<br/>**thumbnail**: Available thumbnail sizes. Values can be `sm`, `m`, `xl`.<br/>**resolution**: returns `height` and `width` of original photo<br/>**orientation**: Orientation of file. Value seems to be integer.<br/>**video_convert**: ???<br/>**video_meta**: ???<br/>**provider_user_id**: ???<br>**exif**: Returns keys as `aperture`, `camera`, `exposure_time"`, `focal_length`, `iso`, `lens`.</br>**tag**: Returns keys `id` and `name` for every tag.</br>**description**: Returns exif description.</br>**gps**: Coordinates returned as values `latitude` and `longitude`.</br>**geocoding_id**: Geocoding id for internal database ???</br>**address**: Address calculated from gps coordinates.</br>**person**: Returns person object.<br/>Example: `&additional["thumbnail","resolution"]` | String ||

### Example:
```
https://<IP_ADDRESS>/photo/webapi/entry.cgi?api=SYNO.Foto.Browse.Item&version=1&method=list&offset=0&limit=100&folder_id=1989&additional=["thumbnail","resolution","orientation","video_convert","video_meta","provider_user_id","exif","tag","description","gps","geocoding_id","address","person"]
```

### Response:
`<data>` object definitions:

| Parameter | Type | Description |
| --- | --- | --- |
| filename | String | Name of file |
| filesize | Integer | Size of file |
| folder_id | Integer | ID of folder |
| id | Integer | ID of file |
| indexed_time | Linux timestamp in second | Timestamp of indexing the file |
| live_type | String | i.e.: photo (in case of iPhone live photos) |
| owner_user_id | Integer | UID of owner |
| time | Linux timestamp in second | Time the photo was taken. Not an absolute instant, see the notes below the `<additional>` table |
| type | String | Type of data<br/><b>photo:</b> Photo<br/><b>video:</b> Video<br/><b>live:</b> iPhone live photos |

`<additional>` object definitions:
| Parameter | Type | Description |
| --- | --- | --- |
| orientation | Integer | EXIF orientation value |
| orientation_original | Integer | Orientation value of the file. It appears to differ from `orientation` once a photo was rotated in Photos |
| resolution | object with keys `height` and `width` as Integer | Height and width in display orientation: a portrait photo with `orientation` 6 reports the portrait size. Do not apply `orientation` to it again |
| thumbnail | object with keys `unit_id`, `cache_key`, `preview`, `sm`, `m`, `xl` | Returns available thumbnail data of photo. `unit_id` is the ID used for thumbnails and downloads and can differ from the item `id` |
| video_convert | Array of objects with key `quality` | Playable renditions the NAS offers. We saw `high`, `orig_h264` and `medium`. An empty array means no conversion is available |
| video_meta | object with key `duration` (and others) | `duration` is in milliseconds |
| provider_user_id | | ??? | 
| exif | Returns keys as `aperture`, `camera`, `exposure_time"`, `focal_length`, `iso`, `lens`. | Exif data. All values are strings meant for display, for example `"F2.8"`, `"1/25 s"`, `"9.0 mm"` and `"1250"` in one sample; parse defensively. Items without EXIF (for example screenshots and videos) return empty strings instead of omitting the keys | 
| tag | Returns object(s) with keys `id` and `name` | | 
| description | String | Exif description | 
| gps | Returns object with keys `latitude` and `longitude`. | GPS coordinates | 
| geocoding_id | Integer | For internal database ??? | 
| address | Returns object with keys `city`, `city_id`, `country`, `country_id`, `county`, `county_id`, `district`, `district_id`, `landmark`, `landmark_id`, `route`, `route_id`, `state`, `state_id`, `town`, `town_id`, `village`, `village_id | Address calculated from gps coordinates. | 
| person | Returns person object(s) | Information about tagged person(s). | 
| folder | String | Full slash-prefixed folder path of the item (Personal Space). Also accepted by `get` |
| favorite | Boolean | `true` for favorited items; the key is absent otherwise |

### Notes from Synology Photos 1.9.1 (`SYNO.Foto.Browse.Item` version 7)
The examples above use version 1. The following was observed with version 7 as sent by the Photos web client.

- **`time` is capture-local wall-clock time.** The integer encodes the date and clock time the photo shows, as if it were UTC, without a timezone. Decode it with a GMT calendar to get the time shown in the photo. Converting it to the viewer's timezone shifts the photo. `start_time` and `end_time` use the same encoding, and the timeline day boundaries are GMT days.
- **Use `additional.thumbnail.unit_id`, not `id`, for `Thumbnail` and `Download`.** In one capture of 1,055 items, `unit_id` differed from `id` for 739 of them.
- **`album_id` (integer) lists the items of one of your own albums.** It combines with `offset`, `limit`, `sort_by`, `start_time`/`end_time` and `favorite_only`. `SYNO.Foto.Browse.Timeline` `get` accepts `album_id` without an error but ignores it and describes the whole library.
- **There is no `total`.** An empty `list` past the end is the end-of-data signal. `limit=1000` and `limit=5000` both worked on an album with 987 items.
- **`method=get` takes an `id` array** (`id=[12]`, even for one item) and returns items in request order, repeating duplicated IDs. IDs that do not exist, or were deleted, are silently omitted without an error or marker. 100, 500 and 1000 IDs per request worked.
- **`additional` is optional per item.** Keys that do not apply (`gps` for an unlocated photo, `favorite` for an unfavorited one) are omitted.

### Example:
```YAML
{
   "data":{
      "list":[
         {
            "additional":{
               "orientation":1,
               "orientation_original":1,
               "resolution":{
                  "height":4000,
                  "width":6000
               },
               "thumbnail":{
                  "cache_key":"80716_1633859830",
                  "m":"ready",
                  "preview":"broken",
                  "sm":"ready",
                  "unit_id":80716,
                  "xl":"ready"
               }
            },
            "filename":"IMG_20210723_201314.JPG",
            "filesize":13078975,
            "folder_id":1989,
            "id":80716,
            "indexed_time":1633867030761,
            "owner_user_id":2,
            "time":1627071194,
            "type":"photo"
         },
         {
            "additional":{
               "orientation":1,
               "orientation_original":1,
               "resolution":{
                  "height":4000,
                  "width":6000
               },
               "thumbnail":{
                  "cache_key":"80715_1633859830",
                  "m":"ready",
                  "preview":"broken",
                  "sm":"ready",
                  "unit_id":80715,
                  "xl":"ready"
               }
            },
            "filename":"IMG_20210723_201307.JPG",
            "filesize":14224627,
            "folder_id":1989,
            "id":80715,
            "indexed_time":1633867030229,
            "owner_user_id":2,
            "time":1627071187,
            "type":"photo"
         },
...
         {
            "filename":"IMG_20210918_154606.MOV",
            "filesize":40551327,
            "folder_id":1990,
            "id":80759,
            "indexed_time":1633870760297,
            "owner_user_id":2,
            "time":1631979966,
            "type":"video"
         },
...
         {
            "additional":{
               "orientation":1,
               "orientation_original":1,
               "resolution":{
                  "height":4000,
                  "width":6000
               },
               "thumbnail":{
                  "cache_key":"80714_1633859830",
                  "m":"ready",
                  "preview":"broken",
                  "sm":"ready",
                  "unit_id":80714,
                  "xl":"ready"
               }
            },
            "filename":"IMG_20210723_193530.JPG",
            "filesize":12873598,
            "folder_id":1989,
            "id":80714,
            "indexed_time":1633867029955,
            "owner_user_id":2,
            "time":1627068930,
            "type":"photo"
         }
      ]
   },
   "success":true
}
```
