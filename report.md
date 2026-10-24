# Nursery IoT Data Integration: Report

Chasity Goff, CSY345-01, Fall 2026

## 1. The three datasets

**batch101.csv (CSV).** I think this file came from a badge reader at the room doors. A row is only written when someone is detected, so it is event-based and not on a schedule. One record is one person detected in one room at one time. It has 40 records and these fields: Time, Date, Room, PersonID, PersonType and AccessStatus (which I added in Checkpoint 2). It covers 5:12 PM to 8:44 PM on 03/11/2026 in four rooms. It does not say whether the person was entering or leaving.

**stream101.json (JSON).** I think this file came from a room sensor that checks the temperature and whether the door is open or closed. It takes a reading on a schedule, every 30 minutes from 5:00 PM to 9:00 PM, for each of the four rooms. One record is one room at one time. It has 36 records (9 time slots x 4 rooms) and these fields: dateTime, roomNumber, roomTempF (degrees Fahrenheit), roomHumidity (added in Checkpoint 2, empty in every record) and doorStatus (open or closed).

**feed101.xml (XML).** I think this file came from a patient vital-signs monitor. It takes a reading every 5 minutes from 17:00 to 21:00 for four patients (P1001 to P1004). One record is one patient at one time. It has 196 records (49 time slots x 4 patients) and these fields: date, time, patientId, heartRate (beats per minute), breathingRate (breaths per minute), temperature (degrees Fahrenheit) and medicalCondition (added in Checkpoint 2, empty in every record).

## 2. Questions the integrated file can answer

My integrated file puts the badge scans, the room sensor readings and the patient vitals in the same row. That means it can answer questions that need more than one file. Here are three examples.

**1. Was the door open or closed when someone scanned into a room, and what was the room temperature?** This needs the badge file and the room sensor file. The badge file only tells me who scanned in and when. The room sensor file only tells me the door and temperature, with no idea who was there. In my file, the nurse N2002 scanned into room 345 at 6:30 PM, the door was open, and the room was 71.8°F.

**2. What was the patient's heart rate and breathing rate when a doctor, nurse or parent came into the patient's room?** This needs the badge file and the vitals file. The badge file doesn't have any vital signs, and the vitals file doesn't say who came into the room. In my file, the doctor D3001 scanned into room 334 at 6:14 PM, and the patient's most recent reading (6:10 PM) was a heart rate of 143 and a breathing rate of 44.

**3. When someone came into the patient's room, what was the room like and how was the patient doing?** This needs all three files. In my file, the parent G4003 scanned into room 334 at 6:16 PM. The door was closed, the room was 72.4°F, and the patient's most recent reading (6:15 PM) was a heart rate of 141, a breathing rate of 44 and a temperature of 98.4°F. No single file has all of that in one place.

## 3. Why I chose CSV

I chose CSV for my integrated file. My file is one flat table: one row for each badge scan, with the same 13 columns on every row. CSV fits that well because it is just rows and columns. I can open it in Excel and read it easily, and I had already worked with a CSV in this project when I edited the badge file in Checkpoint 2.

I did not choose JSON because every value has a label that repeats in every record (like "roomTempF": 72.4). That makes the file longer and harder to scan, even with only 12 rows. I did not choose XML for the same reason. It puts an opening and a closing tag around every value, so it would be even wordier. JSON and XML are better when data is nested or when records have different fields, but my data is flat.

The downside of CSV is that it doesn't store data types or units. To make up for that, I put the units in the column names (like `room_temp_f` and `heart_rate_bpm`) and wrote the types in the data dictionary.

## 4. The integrated dataset

**What one record is.** One record is one badge scan of a person into a room. It also has what the room was like at that moment (temperature and whether the door was open or closed). If the scan was in the patient's room, it also has the patient's heart rate, breathing rate and body temperature.

**The fields.** There are 13 fields in three groups. The data dictionary describes each one.
- From the badge file (CSV): `event_datetime`, `room`, `person_id`, `person_type`, `access_status`.
- From the room sensor file (JSON): `room_reading_time`, `room_temp_f`, `door_status`.
- From the vitals file (XML): `patient_id`, `vitals_reading_time`, `heart_rate_bpm`, `breathing_rate_per_min`, `patient_temp_f`.

**How the fields relate.**
- **Room number** links the badge file and the room sensor file. The badge file says 10305 and the room sensor says 305. After I took off the "10", they match.
- **Patient ID** links the badge file and the vitals file. The only patient who is in both is P1002, and the badge file only ever shows P1002 in room 334. So I assumed P1002 is the patient in room 334, and every scan in room 334 gets P1002's vitals. The data does not say this directly, so it is an assumption.
- **Time** lines the three files up. They were not recorded at the same moments (badge scans at random times, room readings every 30 minutes, vitals every 5 minutes). For each scan, I used the most recent room reading and the most recent vitals reading at or before the scan.

**How I chose the sample.** I used every badge scan from 6:00 PM to 7:00 PM on 03/11/2026. That is one continuous hour and 12 of the 40 scans. I picked it because it is the busiest hour (the hours starting at 5, 7 and 8 PM had 9, 8 and 11 scans). It has scans in all four rooms, it has both open and closed doors, and it includes a scan by the patient. The room readings (6:00 and 6:30) and vitals readings I matched to all fall inside the same hour.

## 5. Data cleaning and normalization

- **Room numbers.** I took the leading "10" off the badge file rooms (10305 became 305) so they match the room sensor file. This is what let me link the two files.
- **Date and time.** The badge file used `5:12 PM` and `03/11/2026` in two columns, the room sensor used `03/11/2026 05:00 PM` in one field, and the vitals file used `2026-03-11` and `17:00` in two tags. I changed all of them to one format, `YYYY-MM-DD HH:MM` in 24-hour time, so the times can be compared across files.
- **Units.** The badge file has no units, the room sensor has F in its field name, and the vitals file has unit labels. All of the temperatures were already in Fahrenheit, so I did not convert anything. I put the units in the column names (`room_temp_f`, `heart_rate_bpm`, `breathing_rate_per_min`, `patient_temp_f`) so they don't get lost in a CSV.
- **Missing values.** The patient columns are blank for scans in rooms other than 334, because no patient is linked to those rooms. I left them blank instead of putting 0, because 0 would look like a real measurement. I left out humidity and medical condition completely because they are empty in every record of the source files.
- **Stray space.** One row in the full badge file (5:24 PM) has a space after `Granted`. That row is outside my sample hour, so it is not in the integrated file.
- **Renaming.** I renamed the fields to one style, lowercase with underscores (for example `PersonID` became `person_id`, `roomTempF` became `room_temp_f`, `heartRate` became `heart_rate_bpm`).

## 6. Trade-offs

**What I gained.** The biggest thing I gained is that the answers are in one place. For each badge scan, I can see who came in, what the room was like and how the patient was doing, without looking through three files that have different room numbers, date formats and time schedules. Cleaning the data also means the files can finally be compared, because they use the same room numbers, the same date and time format, and the units are in the column names.

**What I lost or simplified.**
- **The time matching is not exact.** The room sensor only reads every 30 minutes, so I used the most recent reading before each scan. In my sample the oldest one was 28 minutes old (the 6:28 PM scan used the 6:00 PM reading), so the door or temperature could have changed in between. The vitals were closer, between 1 and 4 minutes old.
- **I only used a small part of the data.** My sample is 12 of the 40 badge scans, from about 1 hour out of 4 hours of data. I also only used 4 of the 196 vitals readings, so a pattern in my sample might not be true for the whole evening.
- **I left out 3 of the 4 patients.** P1001, P1003 and P1004 never show up in the badge file, so I could not link them to a room.
- **I had to make an assumption.** I assumed P1002 is the patient in room 334. Because of that, anyone who scanned into room 334 gets P1002's vitals, even though the data doesn't say the patient was in the room at that moment.
- **Some columns are mostly blank.** 8 of my 12 rows have no patient information, because those scans were in other rooms. I also left out humidity and medical condition completely, since they were empty in every record.
