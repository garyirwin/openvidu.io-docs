<h2 id="section-title">REST API Pro</h2>
<hr>

OpenVidu Pro provides all of [OpenVidu CE REST operations](reference-docs/REST-API/){:target="_blank"}, but also includes some extra REST operations of its own.

All REST operations have in common the header referred to authorization. It is implemented via Basic Auth, and it is as simple as applying Base64 encoding to the username (always "OPENVIDUAPP") and the password (your **secret** shared with openvidu-server). If authorization header is wrong, every call to any REST API operation will return HTTP status `401`.

For example, for secret "MY_SECRET", the final valid HTTP header would be

> `Authorization: Basic T1BFTlZJRFVBUFA6TVlfU0VDUkVU`

### List of available operations
<br>

- Get Media Node info: [**GET /pro/media-nodes/&lt;MEDIA_NODE_ID&gt;**](#get-promedia-nodesltmedia_node_idgt)
- Get all Media Nodes info: [**GET /pro/media-nodes**](#get-promedia-nodes)
- Add new Media Node: [**POST /pro/media-nodes**](#post-promedia-nodes)
- Remove Media Node: [**DELETE /pro/media-nodes/&lt;MEDIA_NODE_ID&gt;**](#delete-promedia-nodesltmedia_node_idgt)
- Modify Media Node: [**PATCH /pro/media-nodes/&lt;MEDIA_NODE_ID&gt;**](#patch-promedia-nodesltmedia_node_idgt)
- Force Media Nodes autodiscovery: [**PUT /pro/media-nodes**](#put-promedia-nodes)
- Restart OpenVidu Pro: [**POST /pro/restart**](#post-prorestart)
- Get OpenVidu Pro active configuration: [**GET /pro/config**](#get-proconfig)

---

### GET `/pro/media-nodes/<MEDIA_NODE_ID>`

| _GET MEDIA NODE INFO_ | _PARAMETERS_                                                                                                           |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| **Operation**       | GET                                                                                                                      |
| **URL**             | https://&lt;YOUR_OPENVIDUSERVER_IP&gt;/pro/media-nodes/&lt;MEDIA_NODE_ID&gt;                                             |
| **Query params**    | `load`, `sessions`, `extra-info`                                                                                         |
| **Headers**         | Authorization: Basic _EncodeBase64(OPENVIDUAPP:&lt;YOUR_SECRET&gt;)_<br/>Content-Type: application/x-www-form-urlencoded |
| **Sample return**   | `{"id": "kms_RicdrF9x", "environmentId": "5372e1c954fd54c13706f476236", "ip": "172.17.0.4", "uri": "ws://172.17.0.4:8888/kurento", "connected": true, "connectionTime": 1583753233620, "sessions": [], "load": 0.0, "status": "running"}` |

> **Query params**
>
> - `load` _(optional boolean, default to false)_ : whether to return Media Node load or not. The value will depend on [configuration property `openvidu.pro.cluster.load-strategy`](openvidu-pro/reference-docs/openvidu-server-pro-params){:target="_blank"}
> - `sessions` _(optional boolean, default to false)_ : whether to return session information along Media Node information or not. Only sessions hosted in this Media Node will be retrieved. Session information has same format as returned by method [GET /api/sessions/&lt;SESSION_ID&gt;](reference-docs/REST-API/#get-apisessionsltsession_idgt){:target="_blank"}
> - `extra-info` _(optional boolean, default to false)_ : whether to return extra information about the Media Node or not. Only for advanced users
>
> ---
>
> _Encode query params in the url like this:_ <br> _https://&lt;YOUR_OPENVIDUSERVER_IP&gt;/pro/media-nodes**?load=true&sessions=false&extra-info=false**_

<div></div>

> **Returned JSON**
>
> - `id`: Media Node unique identifier. Use it to perform other REST operations on this Media Node
> - `environmentId`: Media Node identifier dependent on the deployment environment. For example, an AWS EC2 machine id if the cluster is deployed in AWS
> - `ip`: Media Node IP
> - `uri`: Media Node URI endpoint. This is the URI where OpenVidu Server will establish a connection with the Media Node
> - `connected`: whether OpenVidu Server Pro is connected to this Media Node or not. This property may be set to false if there's an unexpected disconnection of the Media Node
> - `connectionTime`: when did OpenVidu Server establish the connection with the Media Node (in UTC milliseconds)
> - `disconnectionTime`: when did OpenVidu Server lose its connection to the Media Node (in UTC milliseconds). Only defined if `connected` is false
> - `sessions`: session information of this Media Node. This property is an array of objects with the exact same format as the response returned by method [GET /api/sessions/&lt;SESSION_ID&gt;](reference-docs/REST-API/#get-apisessionsltsession_idgt){:target="_blank"}. Only available if query param `sessions` is set to true
> - `load`: load of the Media Node. Only available if query param `load` is set to true
> - `status`: status of the Media Node (see [Media Node statuses](openvidu-pro/reference-docs/openvidu-server-pro-cdr/#media-node-statuses){:target="_blank"})
> - `kurentoInfo`: object with extra advanced information about this instance of Kurento Media Server (version, modules, memory usage...). Only available if query param `extra-info` is set to true (for advanced users, subject to change)

<div></div>

> **HTTP responses**
>
> - `200`: the Media Node information has been successfully retrieved
> - `404`: no Media Node exists for the passed MEDIA_NODE_ID
> - `501`: [configuration property `openvidu.pro.cluster`](openvidu-pro/reference-docs/openvidu-server-pro-params){:target="_blank"} is set to false. You need to enable cluster mode to be able to manage Media Nodes

---

### GET `/pro/media-nodes`

| _LIST MEDIA NODES INFO_ | _PARAMETERS_                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| **Operation**       | GET                                                                                                                      |
| **URL**             | https://&lt;YOUR_OPENVIDUSERVER_IP&gt;/pro/media-nodes                                                                   |
| **Query params**    | `load`, `sessions`, `extra-info`                                                                                         |
| **Headers**         | Authorization: Basic _EncodeBase64(OPENVIDUAPP:&lt;YOUR_SECRET&gt;)_<br/>Content-Type: application/x-www-form-urlencoded |
| **Sample return**   | ```{"numberOfElements": 1, "content": [ {"id": "KMS-XU5ZRM", "uri": "ws://localhost:8888/kurento", "ip": "localhost", "connected": true, "connectionTime": 1562744478463, "load": 0} ]}``` |

> **Query params**
>
> - `load` _(optional boolean, default to false)_ : whether to return Media Nodes load or not. The value will depend on [configuration property `openvidu.pro.cluster.load-strategy`](openvidu-pro/reference-docs/openvidu-server-pro-params){:target="_blank"}
> - `sessions` _(optional boolean, default to false)_ : whether to return session information along Media Nodes information or not.  Media Node will be retrieved. Session information has same format as returned by method [GET /api/sessions/&lt;SESSION_ID&gt;](reference-docs/REST-API/#get-apisessionsltsession_idgt){:target="_blank"}
> - `extra-info` _(optional boolean, default to false)_ : whether to return extra information about the Media Nodes or not. Only for advanced users
>
> ---
>
> _Encode query params in the url like this:_ <br> _https://&lt;YOUR_OPENVIDUSERVER_IP&gt;/pro/media-nodes**?load=true&sessions=false&extra-info=false**_

<div></div>

> **Returned JSON**
>
> - `numberOfElements`: total number of registered Media Nodes
> - `content`: array of Media Nodes. Each object has the same structure as defined in the returned JSON of [**GET /pro/media-nodes/&lt;MEDIA_NODE_ID&gt;**](#get-promedia-nodesltmedia_node_idgt)

<div></div>

> **HTTP responses**
>
> - `200`: Media Nodes information has been successfully retrieved
> - `501`: [configuration property `openvidu.pro.cluster`](openvidu-pro/reference-docs/openvidu-server-pro-params){:target="_blank"} is set to false. You need to enable cluster mode to be able to manage Media Nodes

---

### POST `/pro/media-nodes`

| _NEW MEDIA NODE_  | _PARAMETERS_                                                                                            |
| ----------------- | ------------------------------------------------------------------------------------------------------- |
| **Operation**     | POST                                                                                                    |
| **URL**           | https://&lt;YOUR_OPENVIDUSERVER_IP&gt;/pro/media-nodes                                                  |
| **Query params**  | `wait`                                                                                                  |
| **Headers**       | Authorization: Basic _EncodeBase64(OPENVIDUAPP:&lt;YOUR_SECRET&gt;)_<br/>Content-Type: application/json |
| **Body**          | ```{"uri": "MEDIA_NODE_URI"}```                                                                         |
| **Sample return** | ```{ "id": "kms_LrMRlL42", "ip": "172.17.0.5", "uri": "ws://172.17.0.5:8888/kurento", "connected": true, "connectionTime": 1583769116854, "environmentId": "489ed9c4b4d761699dc93", "status": "running" }``` |

> **Query params**
>
> - `wait` _(optional boolean, default to false)_ : whether to wait until the new Media Node reaches `running` status or not. Setting this property to true basically makes this method synchronized. You will not receive an answer until the Media Node is properly running or an error is thrown.
>
> ---
>
> _Encode query params in the url like this:_ <br> _https://&lt;YOUR_OPENVIDUSERVER_IP&gt;/pro/media-nodes**?wait=true**_

<div></div>

> **Body parameters**
>
> ---
>
> - **uri** _(mandatory string only if `openvidu.pro.cluster.mode` is `manual`)_ : the websocket endpoint of a running Media Node. For a Kurento Media Server, it should be something similar to `ws://media.server.ip:8888/kurento`. **This property is only necessary and is only taken into account if [openvidu.pro.cluster.mode](openvidu-pro/reference-docs/openvidu-server-pro-params){:target="_blank"} is set to `manual`**. For mode `auto` a new Media Node will be automatically launched ignoring parameter `uri`

<div></div>

> **Returned JSON**
>
> Same JSON response as defined for [**GET /pro/media-nodes/&lt;MEDIA_NODE_ID&gt;**](#get-promedia-nodesltmedia_node_idgt) (with all its query params to their default value). If query param `wait` is set to false, most of its properties will be null. All properties will be defined only after the Media Node reaches `running` status.

<div></div>

> **HTTP responses**
>
> - `200`: the Media Node has been successfully added
> - `400`: problem with some body parameter
> - `404`: the Media Node is not within reach of OpenVidu Server. This simply means that OpenVidu cannot establish a connection with it. This may be caused by multiple reasons: wrong IP, port or path, a network problem, too strict a proxy configuration...
> - `409`: the Media Node was already registered in OpenVidu Server
> - `501`: configuration property [openvidu.pro.cluster](openvidu-pro/reference-docs/openvidu-server-pro-params){:target="_blank"} is set to false, or is true but property [`openvidu.pro.cluster.mode`](openvidu-pro/reference-docs/openvidu-server-pro-params){:target="_blank"} is set to `manual` and no `uri` parameter was passed in the body request.
> - `502`: the process of launching a new Media Node instance failed. Only possible if property [`openvidu.pro.cluster.mode`](openvidu-pro/reference-docs/openvidu-server-pro-params){:target="_blank"} is set to `auto`

---

### DELETE `/pro/media-nodes/<MEDIA_NODE_ID>`

| _REMOVE MEDIA NODE_ | _PARAMETERS_                                                                                                            |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------ |
| **Operation**      | DELETE                                                                                                                   |
| **URL**            | https://&lt;YOUR_OPENVIDUSERVER_IP&gt;/pro/media-nodes/&lt;MEDIA_NODE_ID&gt;                                             |
| **Query params**   | `deletion-strategy`                                                                                                      |
| **Headers**        | Authorization: Basic _EncodeBase64(OPENVIDUAPP:&lt;YOUR_SECRET&gt;)_<br/>Content-Type: application/x-www-form-urlencoded |
| **Sample return**  | _Returns nothing_                                                                                                        |

> **Query params**
>
> - `wait` _(optional boolean, default to false)_ : whether to wait until the new Media Node reaches `terminated` status or not. Setting this property to true basically makes this method synchronized. You will not receive an answer until the Media Node is fully terminated or an error is thrown.
> - `deletion-strategy` _(optional string, default to "if-no-sessions")_ : how should OpenVidu Pro proceed with the Media Node deletion. Can be:
>     - `now`: OpenVidu Pro will remove the Media Node immediately. All OpenVidu sessions hosted by this Media Node will be closed with reason `mediaServerDisconnect` (all streams, participants and recordings of all these sessions will be stopped with this same reason)
>     - `if-no-sessions` _(default value)_ : if there's any OpenVidu session initialized inside of this Media Node, then this operation will fail with HTTP status `409`. If the Media Node has no ongoing sessions, then OpenVidu Pro will remove it immediately, returning status `204`
>     - `when-no-sessions`: if there's any OpenVidu session initialized inside this Media Node, then it will not be immediately deleted, but instead will be set to `waiting-idle-to-terminate` status. This status means the Media Node is under quarantine and no more sessions will be initialized inside of it. Whenever the last session of this Media Node is destroyed (no matter the reason), then it will be automatically deleted. The response status will be `202` if this operation changed the Media Node to `waiting-idle-to-terminate` status and `204` if there were no ongoing sessions inside the Media Node and therefore OpenVidu Pro has deleted it.
>
> ---
>
> _Encode query params in the url like this:_ <br> _https://&lt;YOUR_OPENVIDUSERVER_IP&gt;/pro/media-nodes/&lt;MEDIA_NODE_ID&gt;**?wait=false&deletion-strategy=now**_

<div></div>

> **HTTP responses**
>
> - `202`: if query parameter `deletion-strategy` is set to `when-no-sessions`, then it means that the Media Node to be deleted has ongoing sessions inside of it. Media Node status has been set to `waiting-idle-to-terminate`
> - `204`: the Media Node was successfully removed
> - `404`: no Media Node exists for the passed MEDIA_NODE_ID
> - `409`: if query parameter `deletion-strategy` is set to `if-no-sessions`, then it means that the Media Node to be deleted has ongoing sessions inside of it. No Media Node deletion will take place at all.
> - `501`: configuration property [openvidu.pro.cluster](openvidu-pro/reference-docs/openvidu-server-pro-params){:target="_blank"} is set to false. You need to enable cluster mode to be able to manage Media Nodes
> - `502`: error while terminating the Media Node instance. Only possible if property [`openvidu.pro.cluster.mode`](openvidu-pro/reference-docs/openvidu-server-pro-params){:target="_blank"} is set to `auto`

---

### PATCH `/pro/media-nodes/<MEDIA_NODE_ID>`

| _MODIFY MEDIA NODE_       | _PARAMETERS_                                                                                            |
| ------------------------- | ------------------------------------------------------------------------------------------------------- |
| **Operation**             | PATCH                                                                                                   |
| **URL**                   | https://&lt;YOUR_OPENVIDUSERVER_IP&gt;/pro/media-nodes/&lt;MEDIA_NODE_ID&gt;                            |
| **Headers**               | Authorization: Basic _EncodeBase64(OPENVIDUAPP:&lt;YOUR_SECRET&gt;)_<br/>Content-Type: application/json |
| **Body**                  | ```{"status": "MEDIA_NODE_STATUS"}```                                                                            |
| **Sample return**         | ```{"id": "KMS-GVM2CW", "uri": "ws://localhost:8888/kurento", "ip": "localhost", "connected": true, "connectionTime": 1562746120857, "quarantined": false}``` |

> **Body parameters**
>
> ---
>
> - **status** _(mandatory string)_ : new desired status of the Media Node. Valid values are `waiting-idle-to-terminate` (from `running` status), `running` (from `waiting-idle-to-terminate`) and `terminating` (from both `running` and `waiting-idle-to-terminate`)

<div></div>

> **Returned JSON**
>
> The modified Media Node. Same JSON response as defined for [**GET /pro/media-nodes/&lt;MEDIA_NODE_ID&gt;**](#get-promedia-nodesltmedia_node_idgt) (with all its query params to their default value)

<div></div>

> **HTTP responses**
>
> - `200`: the Media Node has been successfully modified
> - `204`: the Media Node has not been modified because its status was the same as the provided through body parameters
> - `400`: problem with some body parameter. This will also be the status if you try to set property `status` to an invalid one (`launching`, `failed`, `terminated`)
> - `404`: no Media Node exists for the passed MEDIA_NODE_ID
> - `501`: [configuration property `openvidu.pro.cluster`](openvidu-pro/reference-docs/openvidu-server-pro-params){:target="_blank"} is set to false. You need to enable cluster mode to be able to manage Media Nodes

---

### PUT `/pro/media-nodes`

| _AUTODISCOVER MEDIA NODES_ | _PARAMETERS_                                                                                                      |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| **Operation**       | PUT                                                                                                                      |
| **URL**             | https://&lt;YOUR_OPENVIDUSERVER_IP&gt;/pro/media-nodes                                                                   |
| **Headers**         | Authorization: Basic _EncodeBase64(OPENVIDUAPP:&lt;YOUR_SECRET&gt;)_                                                     |
| **Sample return**   | ```{"numberOfElements": 1, "content": [ "id": "kms_LrMRlL42", "ip": "172.17.0.5", "uri": "ws://172.17.0.5:8888/kurento", "connected": true, "connectionTime": 1583769116854, "environmentId": "489ed9c4b4d761699dc93", "status": "running" ]}``` |

> **Returned JSON**
>
> - `numberOfElements`: total number of newly autodiscovered Media Nodes
> - `content`: array of newly autodiscovered Media Nodes. Each object has the same structure as defined in the returned JSON of [**GET /pro/media-nodes/&lt;MEDIA_NODE_ID&gt;**](#get-promedia-nodesltmedia_node_idgt) (with all its query params to their default value)

<div></div>

> **HTTP responses**
>
> - `200`: autodiscovery process has completed
> - `405`: autodiscovery process is not possible. This may happen if OpenVidu Pro cluster environment is set to `on_premise` and no autodiscover script is available.
> - `501`: [configuration property `openvidu.pro.cluster`](openvidu-pro/reference-docs/openvidu-server-pro-params){:target="_blank"} is set to false. You need to enable cluster mode to be able to manage Media Nodes

---

### POST `/pro/restart`

| _RESTART OPENVIDU PRO_ | _PARAMETERS_                                                                    |
| ---------------------- | ------------------------------------------------------------------------------- |
| **Operation**          | POST                                                                            |
| **URL**                | https://&lt;YOUR_OPENVIDUSERVER_IP&gt;/pro/restart/                             |
| **Headers**            | Authorization: Basic _EncodeBase64(OPENVIDUAPP:&lt;YOUR_SECRET&gt;)_<br/>Content-Type: application/json |
| **Body**               | ```{"openvidu.secret": "MY_SECRET", "openvidu.cdr": true, "openvidu.recording": true, "openvidu.recording.public-access": true, "openvidu.recording.notification": "publisher_moderator", "openvidu.recording.path": "/opt/openvidu/recordings", "openvidu.recording.custom-layout": "/opt/openvidu/custom-layout", "openvidu.recording.autostop-timeout": 120, "openvidu.webhook": true, "openvidu.webhook.endpoint": "http://localhost:7777/webhook/", "openvidu.webhook.headers": [\"Authorization:\ Basic\ T1BFTlZJRFVBUFA6TVlfU0VDUkVU\"], "openvidu.webhook.events": ["recordingStatusChanged"], "openvidu.streams.video.max-recv-bandwidth": 1000, "openvidu.streams.video.min-recv-bandwidth": 300, "openvidu.streams.video.max-send-bandwidth": 1000, "openvidu.streams.video.min-send-bandwidth": 300, "openvidu.pro.stats.monitoring-interval": 30, "openvidu.pro.stats.webrtc-interval": 20}``` |

> **Body parameters**
>
> The body of the POST request is a JSON object with the new [OpenVidu system properties](reference-docs/openvidu-server-params/){:target="_blank"} to be applied on the restart process. Not all of them are available, and a few are exclusive to [OpenVidu Pro](openvidu-pro/reference-docs/openvidu-server-pro-params){:target="_blank"}. The complete list of available properties is listed below
>
> ---
>
> - **openvidu.secret** _(optional string)_ : new secret to secure OpenVidu Pro
> - **openvidu.cdr** _(optional boolean)_ : whether to enable OpenVidu Call Detail Record or not
> - **openvidu.recording** _(optional boolean)_ : whether to enable OpenVidu recording module or not
> - **openvidu.recording.public-access** _(optional boolean)_ : whether to allow free http access to recorded sessions or not
> - **openvidu.recording.notification** _(optional string)_ : which users should receive the recording events in the client side (`"all"`, `"publisher_moderator"`, `"moderator"` or `"none"`)
> - **openvidu.recording.path** _(optional string)_ : system path where to store the video files of recorded session
> - **openvidu.recording.custom-layout** _(optional string)_ : sytem path where OpenVidu Server should look for custom recording layouts
> - **openvidu.recording.autostop-timeout** _(optional number)_ : timeout in seconds for recordings to automatically stop
> - **openvidu.webhook** _(optional boolean)_ : whether to enable webhook service or not
> - **openvidu.webhook.endpoint** _(optional string)_ : endpoint where OpenVidu Server should sent webhook events. Mandatory if `openvidu.webhook` is true
> - **openvidu.webhook.headers**: _(optional array of strings)_ : HTTP headers OpenVidu Server should attach to each POST message of webhook events
> - **openvidu.webhook.events** _(optional array of strings)_ : events for which a POST message will be sent by OpenVidu Server webhook service
> - **openvidu.streams.video.max-recv-bandwidth** _(optional number)_ : maximum video bandwidth sent from clients to OpenVidu Server, in kbps. 0 means unconstrained
> - **openvidu.streams.video.min-recv-bandwidth** _(optional number)_ : minimum video bandwidth sent from clients to OpenVidu Server, in kbps. 0 means unconstrained
> - **openvidu.streams.video.max-send-bandwidth** _(optional number)_ : maximum video bandwidth sent from OpenVidu Server to clients, in kbps. 0 means unconstrained
> - **openvidu.streams.video.min-send-bandwidth** _(optional number)_ : minimum video bandwidth sent from OpenVidu Server to clients, in kbps. 0 means unconstrained
> - **openvidu.pro.stats.monitoring-interval** _(optional number)_ : interval in seconds for CPU, memory and net usage stats gathering in OpenVidu Server Pro host machine. 0 means no gathering at all
> - **openvidu.pro.stats.webrtc-interval** _(optional number)_ : interval in seconds for WebRTC stats gathering from Kurento Media Server WebRTC endpoints. 0 means no gathering at all

<div></div>

> **HTTP responses**
>
> - `200`: the restarting process has been initialized. All properties are valid and OpenVidu Server should restart properly
> - `400`: there's some problem with a body parameter. The response message will provide further details

<div></div>

> **NOTES**
>
> This method will restart OpenVidu Server Pro with the new provided configuration parameters. The new values will be stored in disk in the configuration file, so you will be able to restart the host without losing your new configuration

---

### GET `/pro/config`

| _GET OPENVIDU PRO CONFIGURATION_   | _PARAMETERS_                                        |
| ----------------- | -------------------------------------------------------------------- |
| **Operation**     | GET                                                                  |
| **URL**           | https://&lt;YOUR_OPENVIDUSERVER_IP&gt;/pro/config                    |
| **Headers**       | Authorization: Basic _EncodeBase64(OPENVIDUAPP:&lt;YOUR_SECRET&gt;)_ |
| **Sample return** | ```{"version": "2.10.0", "openviduPublicurl": "https://localhost:4443/", "openviduCdr": false, "maxRecvBandwidth": 1000, "minRecvBandwidth": 300, "maxSendBandwidth": 1000, "minSendBandwidth": 300, "openviduRecording": true, "openviduRecordingVersion": "2.9.0", "openviduRecordingPath": "/opt/openvidu/recordings/", "openviduRecordingPublicAccess": false, "openviduRecordingNotification": "publisher_moderator", "openviduRecordingCustomLayout": "/opt/openvidu/custom-layout/", "openviduRecordingAutostopTimeout": 120, "openviduWebhook": true, "openviduWebhookEndpoint": "http://localhost:7777/webhook/", "openviduWebhookHeaders": ["Authorization: Basic YWJjZDphYmNk"], "openviduWebhookEvents": ["recordingStatusChanged"], "openviduServerDependencyVersion": "2.10.0", "openviduProStatsMonitoringInterval": 30, "openviduProStatsWebrtcInterval": 30, "openviduProCluster": false, "openviduProClusterLoadStrategy": "streams", "kmsUris": ["ws://localhost:8888/kurento"]}``` |

> **Returned JSON**
>
> - `version`: version of OpenVidu Server Pro
> - `openviduServerDependencyVersion`: version of OpenVidu Server Community Edition upon which this version of OpenVidu Server Pro is built on
> - **Rest of properties**: values given to **[system properties](reference-docs/openvidu-server-params/){:target="_blank"}** on OpenVidu Server Pro launch. These properties are mostly common to method [GET /config](reference-docs/REST-API/#get-config){:target="_blank"} of OpenVidu Server Community Edition. Some of them are [unique for OpenVidu Server Pro](openvidu-pro/reference-docs/openvidu-server-pro-params){:target="_blank"}

<br>