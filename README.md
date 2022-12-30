API proxy to sit between Home Assistant and Tidbyt API to allow for notifications to be sent to the tidbyt via starlark pixlet templates rendered from go templates.  Currently, only one `notify` template exists that renders a centered marquee text(`text`) with defined text color(`textcolor`) and background color(`bgcolor`).  It will render the notifcation accordingly based on the provided parameters.  If an `icon` string parameter is provided, it will look up a slack icon matching the string and render it alongside the text notification. Slack icon code mostly taken from [here](https://github.com/tidbyt/community/tree/main/apps/randomslackmoji) with thanks to [btjones](https://github.com/btjones/)!
It has issue/kinks to work out at the moment but `v0.0.2+` of this proxy is also compatible with [tidbyt-mqtt](https://github.com/tavdog/tidbyt-mqtt) firmware here if you want to go fully local, also with thanks to [tavdog](https://github.com/tavdog/)!

**Note:** At the moment, Each additional template would need some code modification to include it.  I'll probably make this dynamic in the future.

**Further Note:** I haven't bothered implementing auth to this as I prefer to manage that separately.

#### Example Home Assistant setup

#####  `rest_command` definition:
```yaml
rest_command:
  tidbyt-notify:
    url: http://tidbyt:8080/api/notify
    payload: '{"text": "{{ text }}", "textcolor": "{{ textcolor }}", "bgcolor": "{{ bgcolor }}", "icon": "{{ icon }}"}'
    method: POST
  tidbyt-image:
    url: http://tidbyt:8080/api/image
    payload: '{"image": "{{ image }}", "height": "{{ height }}", "width": "{{ width }}"}'
    method: POST    
```

#####  `Service` usage:
```yaml
...
- service: rest_command.tidbyt
  data:
    text: "Banana for scale!"
    textcolor: "#ffffff"
    bgcolor: "#00ff00"
    icon: "Banana"    
...
```

#### Developing templates locally
Copy `.env.example` to `.env` and fill in your tidbyt api key and device id into the respective env vars.
If you use postman and add the `returnimage: true` boolean parameter, it will return the resulting rendered image as a response, useful for testing new templates.  An included postman collection is included for testing of templates too.

```
docker-compose up --build

# curl examples:
curl -k http://localhost:8080/api/notify -d '{"text": "this is a test", "textcolor": "#000000", "bgcolor": "#ffffff", "icon": "parrot"}'

curl -k http://localhost:8080/api/image -d '{"image": "https://<some-image-url>", "width": 15, "height": 15}'
```
