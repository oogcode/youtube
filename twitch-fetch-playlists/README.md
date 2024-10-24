# README

Fetch twitch.tv playlist files.
Supports different resolutions.

### Usage

```bash
python3 -m venv .venv
source ./.venv/bin/activate
pip install poetry
python3 -m poetry install
python3 main.py --help
python3 main.py --streamer $STREAMER_NAME
python3 -m http.server 3000 --directory playlists
```

### How Does it Work

Each stream (if it's not deleted) should have a link that starts with one of following domains.

```python
DOMAINS = [
  "https://vod-secure.twitch.tv/",
  "https://vod-metro.twitch.tv/",
  "https://vod-pop-secure.twitch.tv/",
  "https://d1m7jfoe9zdc1j.cloudfront.net/",
  "https://d1mhjrowxxagfy.cloudfront.net/",
  "https://d1ymi26ma8va5x.cloudfront.net/",
  "https://d2nvs31859zcd8.cloudfront.net/",
  "https://d2vjef5jvl6bfs.cloudfront.net/",
  "https://d3vd9lfkzbru3h.cloudfront.net/",
  "https://dgeft87wbj63p.cloudfront.net/",
  "https://dqrpb9wgowsf5.cloudfront.net/",
  "https://ds0h3roq6wcgc.cloudfront.net/",
]
```

The path for a given stream consists of the streamer id, the streamer name, and the start time.
It also includes a sha1 hash of a string constructed from these attributes.
So just append the path to the possible domains to generate a list of candidate links and check each one.

```python
partial_path = f"{streamer_id}_{streamer_name}_{start_time}"
full_path = f"{sha1(partial_path)}_{partial_path}/chunked/index-dvr.m3u8"
possible_links = []
for domain in DOMAINS:
  possible_link = f"{domain}{full_path}"
  possible_links.append(possible_link)
check_each_link(possible_links)
```

For example, the streamer name is `gmhikaru`, the stream id is `42696178206`, and the unix start time is `1729348117`.

```python
partial_path = "gmhikaru_42696178206_1729348117"
full_path = "4e6ff04815e974fdc75f_gmhikaru_42696178206_1729348117"
possible_link[0] = "https://vod-secure.twitch.tv/4e6ff04815e974fdc75f_gmhikaru_42696178206_1729348117/chunked/index-dvr.m3u8"
```

Other details:

- replace `unmuted` with `muted`
- timestamp sometimes 1 off
- some segments may be missing
