# solarpowered.github.io
Solar Powered is a energy sustainable techology company

#The following Python example shows how to make the request with exponential backoff:


import json
import time
import urllib.error
import urllib.parse
import urllib.request

# The maps_key defined below isn't a valid Google Maps API key.
# You need to get your own API key.
# See https://developers.google.com/maps/documentation/timezone/get-api-key
API_KEY = "YOUR_KEY_HERE"
TIMEZONE_BASE_URL = "https://maps.googleapis.com/maps/api/timezone/json"


def timezone(lat, lng, timestamp):

    # Join the parts of the URL together into one string.
    params = urllib.parse.urlencode(
        {"location": f"{lat},{lng}", "timestamp": timestamp, "key": API_KEY,}
    )
    url = f"{TIMEZONE_BASE_URL}?{params}"

    current_delay = 0.1  # Set the initial retry delay to 100ms.
    max_delay = 5  # Set the maximum retry delay to 5 seconds.

    while True:
        try:
            # Get the API response.
            response = urllib.request.urlopen(url)
        except urllib.error.URLError:
            pass  # Fall through to the retry loop.
        else:
            # If we didn't get an IOError then parse the result.
            result = json.load(response)

            if result["status"] == "OK":
                return result["timeZoneId"]
            elif result["status"] != "UNKNOWN_ERROR":
                # Many API errors cannot be fixed by a retry, e.g. INVALID_REQUEST or
                # ZERO_RESULTS. There is no point retrying these requests.
                raise Exception(result["error_message"])

        if current_delay > max_delay:
            raise Exception("Too many retry attempts.")

        print("Waiting", current_delay, "seconds before retrying.")

        time.sleep(current_delay)
        current_delay *= 2  # Increase the delay each time we retry.


if __name__ == "__main__":
    tz = timezone(39.6034810, -119.6822510, 1331161200)
    print(f"Timezone: {tz}")
