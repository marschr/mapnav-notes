
### Notes and findings on maps/nav for openpilot

#### Rendering:
- Adreno 530 driver on current EON/C2 Android version doesn't seem to support "EXT_external_objects_fd" OpenGL/Vulkan extensions used for IPC GL texture. Could fallback to some shared DMA/ION/SVM like vision does? (maybe benchmark both solutions to check if there are real perf gains) Or even draw directly from UI module? (related discord link: https://discordapp.com/channels/469524606043160576/534138605136773138/730596253720641556)
(OnePlus3 vulkan driver info https://vulkan.gpuinfo.org/displayreport.php?id=3603#extensions)
[![mapnav-ipc](mapnav-ipc.jpg)](https://imgur.com/gallery/8pfHO5X)
**(link on the image for video)**

- Current mapbox-gl-native breaks down when trying to pitch camera below 60 degrees. Also causes maptile requests runaway. Could fix with LODs. Need to investigate other implementations fixes (JS and iOS). This is mainly useful if nav would do AR projections over camera frames.  (related discord link: https://discordapp.com/channels/469524606043160576/534138605136773138/740115817924132884)
[![pitch-issue](pitch-issue.jpg)](https://imgur.com/7M3PaNS)
**(link on the image for video)**

- Current mapbox implementations doesn't seem to support multi-lane rendering, if needed for map visualization then need to implement support for it.

##### Links dump:
- Mabpox Lanes:
	- https://docs.mapbox.com/api/navigation/#lane-object
- OSM Lanes:
	- https://wiki.openstreetmap.org/wiki/Lanes
	- https://wiki.openstreetmap.org/wiki/Key:lanes

- mapbox-gl-native pitch limitation related issues on github:
    - https://github.com/mapbox/mapbox-gl-native/pull/15195
    - https://github.com/mapbox/mapbox-gl-native/issues/15163
    - https://github.com/mapbox/mapbox-gl-native/pull/15230
    - https://github.com/mapbox/mapbox-gl-native/issues/9037
    - https://github.com/mapbox/mapbox-gl-native/issues/6908
    - https://github.com/mapbox/mapbox-gl-native/issues/2190
    - https://github.com/mapbox/mapbox-gl-js/pull/8975
    - https://github.com/mapbox/mapbox-gl-native-android/issues/162


#### Nav:
- Could use overpass to query OSM ways/segments nodes and extract lane info
https://github.com/marschr/comma2k19 (check notebooks/plot_on_map.ipynb )
![Alt](mapbox_osm_overpass.png "openstreetmap nodes plotted onto mapboxgl plugin")

- As for routing, OSM does provide the number of lanes for a given path/way segment and some turn lane restrictions. Mapbox does provide turn lane restrictions and routing APIs. Will openpilot need multi-lane info to navigate on crowded highways (to feed the model/cereal desire inputs) or just "keep right/left" would be enough? Also, C2 GPS positioning with KF seems good enough to localize ego position within lanes.

#### Todo: 
- **[work in progress]** Implement overpass + OSM data local server:
    - Links
        - https://wiki.openstreetmap.org/wiki/Setting_up_a_local_copy_of_the_OpenStreetMap_database,_kept_up_to_date_with_minutely_diffs
        - http://overpass-api.de/
        - http://overpass-api.de/no_frills.html
        - http://overpass-api.de/full_installation.html
        - https://github.com/drolbr/Overpass-API
        - https://wiki.openstreetmap.org/wiki/Overpass_turbo
- **[work in progress]** Prototype frontend with nav/routing controls on QT
    - ![Alt](wip-frontend.png "wip-frontend")
    - [work in progress] PyQt5 offroad port for faster styling and prototyping
    ![Alt](pyqt-ui-protoyping.png "wip-pyqt-ui-prototyping")
    
- Cleanup local codebase and openpilot fork and push to github.
- Hack comma connect to send routes to openpilot (should use athena?)
- Check/implement Valhalla stuff:
    - https://github.com/valhalla/valhalla
    - https://wiki.openstreetmap.org/wiki/Valhalla


##### Other links/stuff:
- https://www.mapzen.com/
- https://github.com/mapzen/
- https://www.interline.io/osm/extracts/
- https://doc.qt.io/qt-5/location-plugin-mapboxgl.html