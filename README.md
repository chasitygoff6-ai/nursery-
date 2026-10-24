# IoT Data Integration: Nursery Scenario

This is my individual project for CSY345-01 (Fall 2026). It uses data from three devices in a nursery, all from the evening of March 11, 2026. First I looked at each of the three files to figure out what was in it. Then I put them together into one file, so someone could answer questions that none of the three files can answer by themselves.

**Author:** Chasity Goff

## The three source files

All three files are in the `source-data` folder.

| File | Format | Device (my guess) | What one record is |
|---|---|---|---|
| `batch101.csv` | CSV | A badge reader | One person scanned into one room at one time |
| `stream101.json` | JSON | A room sensor that checks the temperature and the door | One room's reading every 30 minutes |
| `feed101.xml` | XML | A patient vital-signs monitor | One patient's reading every 5 minutes |

In Checkpoint 2, I added one new field to each file: `AccessStatus` (CSV), `roomHumidity` (JSON) and `medicalCondition` (XML).

## What's in this repository

| File | What it is |
|---|---|
| `README.md` | This file. It explains what is in my repository. |
| `pileline_check_1.md` | My answers to Checkpoint 1, where I figured out what each of the three files has in it. The file name has a typo, but this is the right file. |
| `source-data/` | The three source data files from the table above. |
| `integrated_file.csv` | My integrated file, which puts all three sources together in one table. |
| `data_dictionary.md` | My data dictionary. It lists every column in the integrated file with its type, units and what it means. |
| `report.md` | My report. It covers the three datasets, what questions the integrated file can answer, why I chose CSV, how I cleaned the data, and what I gained and lost by combining it. |

## About my integrated file

- Each row is one badge scan. I added the room temperature and whether the door was open or closed at that time. If the scan was in the patient's room (room 334), I also added the patient's heart rate, breathing rate and temperature.
- For my sample, I used the 12 badge scans between 6:00 PM and 7:00 PM, which is one hour in a row. I picked it because it was the busiest hour (12 of the 40 scans), it has scans in all four rooms, the doors were both open and closed, and the patient was scanned in. My report explains this more.
- I linked the files in three ways. The first is room number: the badge file says 10305 but the room sensor says 305, so I took off the "10". The second is patient ID: P1002 is the only patient who is in both the badge file and the vitals file. The third is time: for each scan, I used the most recent room reading and the most recent vitals reading at or before it.
- I left out humidity and medical condition because they are empty in every record.

## Where to start

1. Read `report.md` for the full explanation.
2. Open `integrated_file.csv` to see the data.
3. Use `data_dictionary.md` to see what each column means.
