# BUGS.md — Task 1

Repository (private): https://github.com/DR-WEN/AI_Programming_Task
Clone URL: https://github.com/DR-WEN/AI_Programming_Task.git


## How I approached it

I started by just running the file to see what happened. That caught the first
bug straight away. After that the program ran without complaining, so the
remaining bugs were not going to announce themselves. I went back and read the
two docstrings properly and treated them as the spec — they describe what each
function is meant to do, so anything the code did differently was a bug. That
is how I found the other four.

Line numbers below refer to the original file I was given.

## Bug 1 - line 30 - `=` instead of `==`

`if response.status_code = 200:`

The program would not run at all. Python gave me a SyntaxError pointing at
this line and suggested `==`. Nothing else executed, so this had to be fixed
before I could see anything else.

`=` assigns a value, `==` compares. A plain `if` needs a comparison.

Fixed to `if response.status_code == 200:`.

## Bug 2 - lines 23-24 - latitude and longitude swapped

```
"latitude": longitude,
"longitude": latitude,
```

Once it ran, the output looked wrong. The `time` field was not Sydney local
time even though `timezone` is set to `auto`, and the temperature did not
match what Sydney was actually doing. I added a `print(params)` before the
request to see what was being sent, and it showed latitude 151.21 and
longitude -33.87. Latitude only goes from -90 to 90, so 151.21 is not a real
latitude — the API was resolving some meaningless location.

The keys were mapped to the wrong arguments.

Fixed to `"latitude": latitude` and `"longitude": longitude`.

## Bug 3 - line 26 - wrong temperature unit

`"temperature_unit": "fahrenheit",`

The docstring says the function requests temperatures in Celsius. The number
coming back was far too high to be Celsius for Sydney. I checked the
Open-Meteo docs and `temperature_unit` takes either `celsius` (the default)
or `fahrenheit`, so the code was contradicting its own documentation.

Fixed to `"temperature_unit": "celsius"`.

## Bug 4 - line 32 - returned only the temperature

`return data["current_weather"]["temperature"]`

The docstring says it returns the full current-weather dict — temperature,
windspeed, winddirection, time and so on — exactly as the API returns it. What
I was getting was a single number. Everything else the API sent was being
thrown away.

The extra `["temperature"]` on the end narrowed it down to one value.

Fixed to `return data["current_weather"]`.

## Bug 5 - line 50 - weather data wrapped in a list

`"data": [data]`

The `save_to_file` docstring says the `data` field holds the weather dict
directly. In the output file it was `"data": [ { ... } ]` — a list with one
item in it. Anyone reading the file would have to do `record["data"][0]`
before they could get at a field, which is not what was described.

The square brackets were wrapping it unnecessarily.

Fixed to `"data": data`.

## Checking the fixes

After all five fixes, `python weather.py` prints `Done.` and writes
`weather_output.json`:

```json
{"time": "2026-08-11T11:00", "interval": 900, "temperature": 16.2, "windspeed": 17.8, "winddirection": 281, "is_day": 1, "weathercode": 0}
```

What I checked:

- `python -m py_compile weather.py` runs clean, so no syntax errors left.
- The `time` field comes back in Sydney local time, which tells me
  `timezone: auto` resolved to the right place, so the coordinates are the
  right way round now.
- The temperature is a sensible Celsius number for Sydney.
- `data` in the JSON file is an object, not an array.
