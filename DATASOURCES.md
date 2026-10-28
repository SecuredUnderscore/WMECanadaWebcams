# Data Sources

Camera data is hardcoded in `script.js` as parallel arrays per source (IDs, latitudes, longitudes, and usually orientations). The image URL is built from the source's base URL plus the camera ID.

| Source | Arrays | Cameras | Camera list from | Image base URL | Last refreshed |
|---|---|---|---|---|---|
| DriveBC | `driveBC*` | 1067 | [DriveBC API: webcams](https://www.drivebc.ca/api/webcams/?) | `https://www.drivebc.ca/images/` | 2026-10-28 |
| City of Vancouver | `vancouver*` | 845 | [Vancouver Open Data: web-cam-url-links](https://opendata.vancouver.ca/explore/dataset/web-cam-url-links/) + each camera page | `https://trafficcams.vancouver.ca/cameraimages/` | 2026-10-28 |
| City of Richmond | `richmond*` | 600 | [City traffic cameras map](https://www.richmond.ca/services/transportation/trafficcameras/default.aspx) | `https://www.richmond.ca/trafficcam/` | 2026-10-08 |
| Township of Langley | `langley*` | 14 | [ArcGIS: TrafficCamerasTourPnts3](https://services5.arcgis.com/frpHL0Fv8koQRVWY/arcgis/rest/services/TrafficCamerasTourPnts3/FeatureServer?f=json) | `https://tolwestus2filestore.blob.core.windows.net/$web/traffic/` | 2026-10-28 |
| City of Chilliwack | `chilliwack*` | 26 | [City camera page](https://www.chilliwack.com/main/page.cfm?id=1528) / `https://api.chilliwack.com/v1/cameras/public`; coordinates placed by hand | `https://api.chilliwack.com/v1/cameras/public/` | 2026-10-28 |
| City of Surrey | `surrey*` | 602 | City of Surrey open data: Traffic Cameras (GeoJSON) | `https://stcameleonprod.blob.core.windows.net/prodcameleon-blob/` | 2026-10-28 |
| City of Toronto | `toronto*` | 1497 | City of Toronto open data: Traffic Camera List (GeoJSON, EPSG:4326) | `https://opendata.toronto.ca/transportation/tmc/rescucameraimages/` | 2026-10-28 |
| York Region, ON | `York*` | 394 | [York Open Data: Traffic MapServer layer 0](https://ww8.yorkmaps.ca/arcgis/rest/services/OpenData/Traffic/MapServer/0/query?f=json&where=1%3D1&returnGeometry=true&outFields=*&outSR=4326) | `https://ww5.yorkmaps.ca/webtrafficimages/loc` | 2026-10-28 |
| City of Ottawa | `Ottawa*` | 402 | `https://traffic.ottawa.ca/map/service/camera` (CITY-owned only) | `https://traffic.ottawa.ca/camera?id=` | 2026-10-28 |
| City of Calgary | `Calgary*` | 219 | [Calgary Open Data: Traffic Cameras (k7p9-kppz)](https://data.calgary.ca/resource/k7p9-kppz.json?$limit=5000) + unlisted live cameras | `https://trafficcam.calgary.ca/` | 2026-10-28 |
| Alberta 511 | `ABProvincial*` | 582 | `https://511.alberta.ca/map/mapIcons/Cameras` + `/tooltip/Cameras/<site>` | `https://511.alberta.ca/map/Cctv/` | 2026-10-28 |
| Ontario 511 | `ONProvincial*` | 1663 | `https://511on.ca/List/GetData/Cameras` (paged, 100 per page) | `https://511on.ca/map/Cctv/` | 2026-10-28 |
| Québec 511 | `QuebecProvincial*` | 680 | Québec 511 open data: Camera (GeoJSON) | `https://www.quebec511.info/Images/Cameras/` | 2026-10-28 |
| Manitoba 511 | `MBProvincial*` | 46 | `https://www.manitoba511.ca/List/GetData/Cameras` (cross-check: `/map/mapIcons/Cameras`) | `https://www.manitoba511.ca/map/Cctv/` | 2026-10-28 |
| Saskatchewan Highway Hotline | `SKProvincial*` | 63 | `https://hotline.gov.sk.ca/List/GetData/Cameras` (cross-check: `/map/mapIcons/Cameras`) | `https://hotline.gov.sk.ca/map/Cctv/` | 2026-10-28 |
| New Brunswick 511 | `NBProvincial*` | 66 | `https://511.gnb.ca/List/GetData/Cameras` (cross-check: `/map/mapIcons/Cameras`) | `https://511.gnb.ca/map/Cctv/` | 2026-10-28 |
| Nova Scotia 511 | `NSProvincial*` | 57 | `https://511.novascotia.ca/List/GetData/Cameras` (cross-check: `/map/mapIcons/Cameras`) | `https://511.novascotia.ca/map/Cctv/` | 2026-10-28 |
| Newfoundland 511 | `NLProvincial*` | 48 | `https://511nl.ca/List/GetData/Cameras` (cross-check: `/map/mapIcons/Cameras`) | `https://511nl.ca/map/Cctv/` | 2026-10-28 |
| PEI 511 | `PEIProvincial*` | 7 | `https://511.gov.pe.ca/List/GetData/Cameras` (cross-check: `/map/mapIcons/Cameras`) | `https://511.gov.pe.ca/map/Cctv/` | 2026-10-28 |

`?` means the original camera list source isn't recorded yet. Fill it in when the source is next refreshed.

Cameras that are listed but temporarily offline still serve a placeholder image ("not available" / "out-of-service"). They are kept. Cameras missing from the source list are dropped.

## Refresh notes

### DriveBC
- `https://www.drivebc.ca/api/webcams/?` returns a JSON list. Each webcam has `id` (the camera ID; image at `https://www.drivebc.ca/images/<id>.jpg`), `orientation` (N/E/S/W and diagonals), `location.coordinates` as [lon, lat], and `links.replayTheDay`.
- Every listed webcam is included, even when `is_on` is false.
- The arrays are ordered IDs, **Longitudes**, Latitudes, Orientations, unlike the other sources.
- `drivebc.ca` redirects to `www.drivebc.ca`, so the base URL uses `www` directly.

### City of Vancouver
- Locations: `https://opendata.vancouver.ca/api/explore/v2.1/catalog/datasets/web-cam-url-links/exports/json` (one record per intersection, with coordinates and a `url` to its camera page).
- Each camera page lists one `<img src="cameraimages/<ID>.jpg">` per direction, under a label such as "North". The camera ID is `<ID>`, and the orientation comes from that label.
- `BurrardBridgeMidspanSouthSouth` is live but not linked from any page, so it is added by hand under Burrard St Bridge.

### City of Richmond
- Source: `https://www.richmond.ca/services/transportation/trafficcameras/default.aspx`, an ASP.NET page with a Mapbox map. Cameras load dynamically, so the page HTML has no static list.
- Camera IDs look like `vdd_<street>_<cross>_<dir>`, served from `https://www.richmond.ca/trafficcam/<ID>.jpg`.
- Not refreshed on 2026-10-28; the data was last updated 2026-10-08.

### Township of Langley
- Query: `.../TrafficCamerasTourPnts3/FeatureServer/0/query?where=1%3D1&outFields=*&outSR=4326&f=json`
- Camera ID is `cameraHyperlink` without `.jpg`. Coordinates come from `lat`/`long`. Orientation comes from the "(Looking North)" text in `name`.

### City of Chilliwack
- `https://api.chilliwack.com/v1/cameras/public` lists every camera (`cameraID`, `name`, `group`), but without coordinates.
- Excluded: MoTI cameras (provincial, already covered by DriveBC) and the Landfill group.
- Coordinates and orientations are placed by hand.

### City of Surrey
- Each feature's `IMAGE` is `.../prodcameleon-blob/<NVR>/<file>.jpg`. `<NVR>` goes in `surreyCameraIDs` and `<file>` in `surreySecondaryCameraIDs`.
- Drop features with no `IMAGE` or whose image returns 404. The data has some case typos (`enc_83A_140_*` is live as `enc_83a_140_*`).
- `TMCNVR10/enc_96_120_pano` is live but missing from the GeoJSON, so it keeps its previous coordinates.

### City of Toronto
- Each location gives `IMAGEURL` (`CameraImages/loc<id>.jpg`, the live view, blank orientation) plus up to four `REFURLn`/`DIRECTIONn` pairs (`ComparisonImages/loc<id><n|e|s|w>.jpg`, static reference views).

### City of Calgary
- Rows: `https://data.calgary.ca/resource/k7p9-kppz.json?$limit=5000` (`/api/views/k7p9-kppz.json` is only metadata). `camera_url.url` is `https://trafficcam.calgary.ca/loc<N>.jpg`, and coordinates are in `point`.
- The open data is incomplete. Probe the `loc<N>` numbers it skips, plus a few above its highest number. Live ones have the intersection burned into the frame, and Alberta 511 often has the same intersection with coordinates.
- Added by hand: `loc128`, `loc171`, `loc180` (coordinates from Alberta 511), and `loc210` (96 Ave/Airport Tr & 60 St NE), `loc223` (50 Ave & Macleod Tr S) (coordinates placed by hand). Dropped as stale (old frames, unlisted): `loc19`, `loc20`, `loc109`, `loc159`, `loc169`.

### City of Ottawa
- `https://traffic.ottawa.ca/map/service/camera` returns `cameras[]` with `id` (used in `camera?id=`), coordinates and `cameraOwner`.
- Only `CITY` cameras are included. `MTO` cameras are provincial highway cameras already covered by Ontario 511.

### York Region
- Query the layer with `outSR=4326` and take coordinates from `geometry`. The `LATITUDE`/`LONGITUDE` attributes are projected and sometimes swapped.
- Camera ID is `FACILITYID`, which matches `photo` (`.../webtrafficimages/loc<ID>.jpg`).

### Alberta 511
- `/map/mapIcons/Cameras` gives camera sites (`itemId`, location). Each site's `/tooltip/Cameras/<itemId>?lang=en` lists its views as `data-lazy="/map/Cctv/<id>"`, each under a `dirDescHeader`.
- Orientation: "X side of HWY" → the opposite direction (the way the camera faces); "NB/SB/EB/WB, …" → facing oncoming traffic; anything else uses the header text as-is.
- The tooltip endpoint rate-limits (HTTP 429). Fetch slowly.
- The 511.alberta.ca `/api/v2/get/cameras` API needs a key.

### Ontario 511
- `List/GetData/Cameras?query=<json>&lang=en-US` caps `length` at 100. Page through with `start` until reaching `recordsTotal`.
- Each site has `images[]` (`id` → `/map/Cctv/<id>`, `description` "Looking North" etc.) and coordinates in `latLng.geography.wellKnownText`.

### Manitoba 511 / Saskatchewan Highway Hotline / New Brunswick 511 / Nova Scotia 511 / Newfoundland 511 / PEI 511
- Same platform and `List/GetData/Cameras` endpoint as Ontario 511. One image per site in most cases. View descriptions carry no direction (blank or "N/A"), so these sources have no orientation array.
- Saskatchewan IDs are now the numeric image IDs (`/map/Cctv/<id>`). The old `<sourceId>--<n>` IDs still resolve, but the platform no longer publishes them.

### Québec 511
- `NumeroCamera` is the still-image ID; its first letter picks the folder (G Gatineau, Q Québec, T TroisRivieres, M Montreal). `IDEcamera` is the replay/video ID.
- Orientation comes from "(northward)" etc. in `DescriptionLocalisationEn`.
- quebec511.info is behind Cloudflare, which blocks scripted requests. Check images in a browser.

## Known issues
- **Calgary / Alberta 511 overlap:** 188 of the 217 Calgary cameras (before loc210/loc223 were added) have an Alberta 511 camera within 150 m, because Alberta 511 republishes City of Calgary cameras. They are probably duplicate pins. Decision (2026-10-28): keep both layers' pins separate; no de-duplication. (Before the 2026-10-28 refresh, the Calgary arrays also didn't line up: 192 IDs but only 100 coordinates, so 92 cameras never loaded.)
