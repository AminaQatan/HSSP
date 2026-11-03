# Security Roster Portal

Duty roster portal for the Security Department of Hilton Salalah Resort, built on Oracle Database and Oracle APEX. It replaces the paper and Excel roster with:

- a roster you fill in by tapping cells
- automatic distribution of days off and shift times
- coverage analysis with charts
- one-page Excel and PDF exports, and printing

**Developer:** Ms. Amina Qatan

## What is in this folder

| Path | What it is |
|---|---|
| `index.html` | The portal. Open it in a browser or publish it with GitHub Pages. |
| `database/hsr_security_roster.sql` | Oracle script: tables, views, the `HSR_ROSTER_PKG` package (sign-in and automatic distribution) and the 1–15 Nov 2026 roster. |

## Features

- **Roster:** 16 staff in 5 teams (Management, Shift Leaders, Security / Operation, Main Gate, CCTV), with posts and the daily on-duty count per team.
- **Quick fill:** pick a value (OFF, AL, PH, PD, C/OFF or a start time) and tap cells to fill them. Tapping a single cell opens a panel that moves to the next day after each choice. Undo is always available.
- **No end date:** the roster runs on in half-months (1st–15th, 16th–end of month). Tap › on the latest period, or choose "+ Next" in the period list, and the next half-month appears with shifts already filled in. When an admin signs in after the last period has ended, the periods up to today are created.
- **Auto-distribute:** creates the next period, or redistributes one. Each team has its own rule:
  - rotating days off (two together, about a week apart)
  - the same days off for everyone (for example Friday and Saturday)
  - taking turns (Main Gate)
  - Leave already entered stays in place, and Shift Leaders get one person on the 10 pm night shift each day.
- **Analysis:** on duty each day by team, shift start times, days off and leave, workload per person, and every day below the minimum cover.
- **Exports:**
  - Excel: the Roster sheet fits one A4 landscape page, plus a Summary sheet.
  - PDF: one A4 landscape page with the work instructions, codes, shift timings and signature lines.
  - Print: prints the same page.
- **Rules & settings:** the 12 work instructions in Arabic, shift timings, admin users, password change, backup and restore.
- Light and dark mode, and phone layout.

## Sign-in

| Role | What they can do |
|---|---|
| Admin (User ID: `Ahmed Qatan`) | Everything: editing, auto-distribute, staff, rules, users |
| Viewer ("View the roster without signing in") | Read the roster and analysis, export and print |

Change the admin password under **Rules & settings › Your password**.

## Try it

- **On your computer:** open `index.html` in a browser.
- **Online with GitHub Pages:**
  1. In the repository, go to Settings › Pages.
  2. Choose Deploy from a branch, branch `main`, folder `/ (root)`, and save.
  3. After a minute the portal is live at `https://<your-username>.github.io/<repository-name>/`.

On GitHub Pages or a computer, changes are saved in that browser. Use **Download backup** and **Restore a backup** under Rules & settings to move them to another device.

## Set up the database (Oracle APEX)

Requirements: Oracle Database 19c or later and Oracle APEX 22.2 or later. A free workspace on [apex.oracle.com](https://apex.oracle.com) works.

1. In your APEX workspace, open SQL Workshop › SQL Scripts › Upload. Choose `database/hsr_security_roster.sql`, then Run.
2. Check the result:
   ```sql
   SELECT * FROM hsr_roster_grid_v WHERE period_id = 1 ORDER BY team_sort, staff_sort;
   ```
3. Distribute the next period:
   ```sql
   DECLARE l_id NUMBER;
   BEGIN
     l_id := hsr_roster_pkg.create_period(DATE '2026-11-16', 15);
     hsr_roster_pkg.auto_distribute(l_id);
     COMMIT;
   END;
   ```
4. To keep the roster going on its own, create the daily job in section 7 (`HSR_KEEP_ROSTER_GOING`). It makes sure today's and the next half-month always exist.
5. Follow section 8 at the end of the script to build the APEX pages:
   - sign-in with `hsr_roster_pkg.authenticate`
   - the roster grid
   - auto-distribute
   - the analysis charts
   - staff and rules

The script can be run again. It drops and recreates only its own `HSR_` objects, so running it again deletes the data in them.

## Privacy

This folder contains staff names and the admin password hash. Keep the repository **private**.
