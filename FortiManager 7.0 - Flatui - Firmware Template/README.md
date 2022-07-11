# FortiManager Firmware Template

Since firmware templates are not availble through the "jsonrpc" API, I'm using the "flatui" API.

Prerequisites :
- "FortiManager Flatui Env" must be imported and selected;
- `ip`, `username` and `password` must be configured in this environment;
- `username` and `password` are the regular ones your are using (or a specific account for this task with enough rights). This doesn't require API login/token since we are using the "flatui" API;

Requests must be executed with the following order :
1. Login : sends credentials to `https://<Hostname/IP address>/cgi-bin/module/flatui_auth`, retrieves `CURRENT_SESSION` and `HTTP_CSRF_TOKEN` and saves them into the environment;
2. XSRF Token : get the `https://<Hostname/IP address>/p/app/` page, retrieves `XSRF-TOKEN` and `csrftoken` and save them into the environment;
3. Enjoy the requests available
4. Logout : logout :)

### Quick notes

I currently have tested methods `get`, `add` and `update` but more (delete ?) should be available.

The `update` method is based on the `oid` and not on the name. This means it should be retrieve before any modification on an existing firmware template.

As usual, requests bodies are JSON formated.
Body dissection :
```JSON
{
    "method": "update", # Request Method
    "params": [
        {
            "data": {
                "fwmprof setting": {
                    "schedule-day": 0, 
                    "description": "I'm a firmware template",
                    "image-source": 1, # Let the FortiGate download the firmware directly from Internet
                    "schedule-end-time": "2022-07-11 00:00",
                    "schedule-start-time": "2022-07-11 00:00",
                    "schedule-type": 5, # In this case, one-time scheduling
                    "enforced version": [ # Per platform mapping
                        {
                            "flags": 0,
                            "platform": "FortiGate-60F", # Model
                            "product": 1, # FortiGate, can also be FSW, FAP, FEX
                            "upgrade-path": 1, # Follow the recommended path
                            "version": "7.0.6-b0366" # Target firmware version
                        },
                        {
                            "flags": 0,
                            "platform": "FortiGate-40F",
                            "product": 1,
                            "upgrade-path": 1,
                            "version": "7.0.6-b0366"
                        }
                    ]
                },
                "name": "HelloWorld", # Name of the firmware template
                "oid": 17109, # Unique ID, mandatory for update method
                "type": "fwmprof"
            },
            "url": "pm/fwmprof/adom/root/HelloWorld" # URL using the ADOM and the template name
        }
    ],
    "id": "1"
}
```