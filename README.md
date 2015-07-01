# Attendee Progress Tool

This quick tool allows you to measure the progress of the attendees through the Chef Fundamentals material in the online course.

1. Add the list of attendees to the **attendees.csv**

This file is parsed for the name of the attendees and their host name that they are using for the event.

2. Run the various rake commands to verify their status

```bash
rake bootstrap           # Verify bootstrapped systems
rake log_level           # Verifying log level is set to :info
rake motd                # Verify motd file has content
rake website             # Verify that a website is available (port 80)
rake refactored_website  # Verify that a second website is available (port 81)
```

## Installation

```
$ bundle install
```