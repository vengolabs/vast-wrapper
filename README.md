# Vengo VAST Wrapper
HTML/JS wrapper for accessing the Vengo VAST tag

## Example VAST Tag
An example of Vengo's VAST tag in XML format can be found on the [Vengo Integration site](https://integration.vengo.tv)

## XML Parsing in Javascript

### MediaFile Element
The MediaFile element is contained within the MediaFiles tag and defines the URL to the creative to be played. In the VAST 2.0 spec, only one MediaFile contained in the MediaFiles tag.

Example XML:
```xml
<MediaFiles>
  <MediaFile id="3a3549b3-20ca-48bf-b73d-7c654df5fc48" width="1920" height="1080" type="video/mp4" delivery="progressive">
    <![CDATA[ https://cdn.staging.vip.vengolabs.com/uploads/vast_sample_creatives/supermarket-2-1920x1080-v2.mp4 ]]>
  </MediaFile>
</MediaFiles>
```

Example parsing:
```js
var mediaFiles = vast.getElementsByTagName("MediaFile");
if (mediaFiles.length > 0) {
  var url = mediaFiles[0].textContent;
}
```

### Impression URL
The Impression URL is defined in the Impression tag. This URL must be requested as close to play time as possible in order to receive credit. There is only one Impression URL per VAST request.

Example XML:
```xml
<Impression>
  <![CDATA[ https://staging-adserver.vengolabs.com/api/v2/ad_play?ad_id=d067ea65-607a-4aee-b1eb-16ab70bcfa67-1644355766&ad_unit_id=sc-000002&organization_id=signage_co ]]>
</Impression>
```

Example parsing:
```js
var impressions = vast.getElementsByTagName("Impression");
if (impressions.length > 0) {
  var url = impressions[0].textContent;
  
  try {
      var xhr = new XMLHttpRequest();
      xhr.open("GET", url, true);
      xhr.send(null);
  } catch (e) {
      console.error(e);
  }
}
```

### Tracking URLs
The Tracking elements are contained within the TrackingEvents tag. Unlike the Impression URL, the tracking URLs are not required to receive credit. However, requesting the URLs are recommended as a best practice.

Example XML:
```xml
<TrackingEvents>
  <Tracking event="start">
    <![CDATA[ https://staging-adserver.vengolabs.com/api/v2/ad_tracking?ad_id=d067ea65-607a-4aee-b1eb-16ab70bcfa67-1644355766&ad_unit_id=sc-000002&creative_id=8f8e6cc5-2b28-4df6-92bf-d82607875bf3&event=start&organization_id=signage_co ]]>
  </Tracking>
  <Tracking event="firstQuartile">
    <![CDATA[ https://staging-adserver.vengolabs.com/api/v2/ad_tracking?ad_id=d067ea65-607a-4aee-b1eb-16ab70bcfa67-1644355766&ad_unit_id=sc-000002&creative_id=8f8e6cc5-2b28-4df6-92bf-d82607875bf3&event=firstQuartile&organization_id=signage_co ]]>
  </Tracking>
  <Tracking event="midpoint">
    <![CDATA[ https://staging-adserver.vengolabs.com/api/v2/ad_tracking?ad_id=d067ea65-607a-4aee-b1eb-16ab70bcfa67-1644355766&ad_unit_id=sc-000002&creative_id=8f8e6cc5-2b28-4df6-92bf-d82607875bf3&event=midpoint&organization_id=signage_co ]]>
  </Tracking>
  <Tracking event="thirdQuartile">
    <![CDATA[ https://staging-adserver.vengolabs.com/api/v2/ad_tracking?ad_id=d067ea65-607a-4aee-b1eb-16ab70bcfa67-1644355766&ad_unit_id=sc-000002&creative_id=8f8e6cc5-2b28-4df6-92bf-d82607875bf3&event=thirdQuartile&organization_id=signage_co ]]>
  </Tracking>
  <Tracking event="complete">
    <![CDATA[ https://staging-adserver.vengolabs.com/api/v2/ad_tracking?ad_id=d067ea65-607a-4aee-b1eb-16ab70bcfa67-1644355766&ad_unit_id=sc-000002&creative_id=8f8e6cc5-2b28-4df6-92bf-d82607875bf3&event=complete&organization_id=signage_co ]]>
  </Tracking>
  ...
</TrackingEvents>
```

Example parsing:
```js
var trackingEvents = vast.getElementsByTagName("Tracking");

function sendTrackingEvents(event) {
    for (var i = 0; i < trackingEvents.length; i++) {
        if (trackingEvents[i].attributes[0].nodeValue == event) {
            try {
                var xhr = new XMLHttpRequest();
                xhr.open("GET", trackingEvents[i].textContent, true);
                xhr.send(null);
            } catch (e) {
                console.error(e);
            }
            break;
        }
    }
}

//Send tracking event at specified durations
sendTrackingEvents("start");
sendTrackingEvents("firstQuartile");
sendTrackingEvents("midpoint");
sendTrackingEvents("thirdQuartile");
sendTrackingEvents("complete");
```
