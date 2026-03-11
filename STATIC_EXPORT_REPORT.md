# MongoDB Content Architecture and Static Export Plan

## 1) Backend to Frontend Data Flow

- `/` route reads `home_popup` via `popup_collection.find_one()` and passes `popup` to `index.html`.
- `/leadership` reads `leadership` and passes `leaders` to `leadership.html`.
- `/blog` reads `blog` and passes `posts` to `blog.html`.
- `/events` reads `events`, builds `events_json` for FullCalendar, and passes both `events` and `events_json` to `events.html`.
- `/gallery` reads `gallery`, groups by album, and passes `albums` to `gallery.html`.
- `/contact` reads `Locations` and passes `locations` to `contact.html`.

Admin routes/pages also read/write Mongo data but are not required for a static public website.

## 2) Collections Found and Frontend Field Usage

Public pages:

- `home_popup`: `image_url` (displayed in index popup modal), `updated_at` (admin view only).
- `leadership`: `name`, `position`, `image_url`.
- `blog`: `title`, `description`, `image_url`, `posted_by`, `created_at`.
- `events`: `title`, `description`, `image_url`, `event_date`, `created_at`.
- `gallery`: `album`, `image_url` (public page); `description`, `date` (admin page).
- `Locations`: `assembly`, `name`, `address`, `phone`, `image_url`.

Non-public or operational collections:

- `contact_messages`: contact inbox/admin only.
- `users`: login/admin only.
- `visits`: analytics counter only.

## 3) Exported JSON Files

Exporter script: `export_static_data.py`

Output directory: `data/`

- `data/home.json`
- `data/leadership.json`
- `data/blog.json`
- `data/events.json`
- `data/gallery.json`
- `data/contact.json`
- `data/export_summary.json`

## 4) JSON Structure (Final)

`home.json`

```json
{
  "popup": {
    "image_url": "https://...",
    "updated_at": "2026-03-11T10:00:00"
  }
}
```

`leadership.json`

```json
{
  "leaders": [
    { "name": "", "position": "", "image_url": "" }
  ]
}
```

`blog.json`

```json
{
  "posts": [
    {
      "title": "",
      "description": "",
      "image_url": "",
      "posted_by": "",
      "created_at": "2026-03-11T10:00:00"
    }
  ]
}
```

`events.json`

```json
{
  "events": [
    {
      "title": "",
      "description": "",
      "image_url": "",
      "event_date": "2026-04-01",
      "created_at": "2026-03-11T10:00:00"
    }
  ],
  "calendar_events": [
    {
      "title": "",
      "description": "",
      "start": "2026-04-01"
    }
  ]
}
```

`gallery.json`

```json
{
  "albums": [
    {
      "name": "Photo",
      "images": [
        {
          "album": "Photo",
          "description": "",
          "date": "2026-03-11",
          "image_url": "https://..."
        }
      ]
    }
  ],
  "images": [
    {
      "album": "Photo",
      "description": "",
      "date": "2026-03-11",
      "image_url": "https://..."
    }
  ]
}
```

`contact.json`

```json
{
  "locations": [
    {
      "name": "",
      "assembly": "",
      "address": "",
      "phone": "",
      "image_url": "https://..."
    }
  ]
}
```

## 5) Static Replacement Strategy (`fetch('/data/...json')`)

Example for events page:

```html
<script>
  async function loadEventsPage() {
    const res = await fetch('/data/events.json');
    const data = await res.json();

    renderEventCards(data.events);

    const calendar = new FullCalendar.Calendar(document.getElementById('eventsCalendar'), {
      initialView: 'dayGridMonth',
      events: data.calendar_events
    });
    calendar.render();
  }

  loadEventsPage();
</script>
```

Equivalent approach per page:

- Home popup: fetch `home.json`.
- Leadership page: fetch `leadership.json`.
- Blog page: fetch `blog.json`.
- Gallery page: fetch `gallery.json`.
- Contact locations: fetch `contact.json`.

## 6) Run Export

```bash
python export_static_data.py
```

