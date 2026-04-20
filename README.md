# sched

A friendly terminal reminder scheduler using `at` and `notify-send`.

Schedule a reminder in plain English and get both a **desktop popup** and an **email** at the exact same time — even after you close the terminal.

## What it does

- Schedules reminders by minutes from now, specific time, or day+time
- Fires a desktop notification via `notify-send`
- Sends an email via `msmtp` at the same moment
- **Repeats every N minutes** indefinitely with the `repeat` flag (self-rescheduling `at` jobs)
- Lists pending reminders with interactive delete (type a number to cancel one)
- Clears all pending reminders with one command
- Jobs survive terminal close and persist across reboots (via `atd` spool)

## Requirements

- `at` / `atd` — job scheduler daemon
- `notify-send` — desktop notifications
- `msmtp` — lightweight SMTP client (configured with a `gmail` account)

## Install

```bash
cp sched ~/.local/bin/sched
chmod +x ~/.local/bin/sched
```

Make sure `~/.local/bin` is on your `PATH`.

## Usage

```bash
sched "Take a break"     25            # in 25 minutes
sched "Finish the parser" 14:30        # at 2:30 PM today
sched "Submit report"    "Monday 09:00" # next Monday at 9 AM
sched "Drink water"      30 repeat     # every 30 minutes, forever

sched list     # show pending reminders; type a number to delete one
sched clear    # cancel all pending at-jobs (stops repeating reminders too)
```

## Key commands

| Command | Effect |
|---|---|
| `sched "msg" <N>` | Remind in N minutes |
| `sched "msg" HH:MM` | Remind at specific time today |
| `sched "msg" "Day HH:MM"` | Remind on a specific day |
| `sched "msg" <N> repeat` | Remind every N minutes, forever |
| `sched list` | Show all pending reminders |
| `sched clear` | Cancel all pending at-jobs (stops repeating reminders) |

## How repeat works

`repeat` uses a self-rescheduling technique: each fired `at` job calls `sched` again before it exits, queuing the next occurrence. Since `at` is one-shot by design, this chains jobs together indefinitely. Use `sched clear` or `atrm <job-id>` to stop the chain.
