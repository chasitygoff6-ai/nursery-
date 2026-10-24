# Data Dictionary: integrated_file.csv 

This is the data dictionary for my integrated file, `integrated_file.csv`. Each row is one time a person scanned their badge to get into a nursery room. I added what the room was like at that time (temperature and door status) and, if the person went into the patient's room, the patient's vital signs. There are 13 fields. A blank cell means there was no data for that row.

| Field name | Data type | Units | What it represents |
|---|---|---|---|
| event_datetime | Date and time (YYYY-MM-DD HH:MM, 24-hour) | none | The date and time the person scanned into the room. This came from the badge file. |
| room | Whole number (an ID, not used for math) | none | The room number where the scan happened (305, 334, 337 or 345). The badge file wrote it with five digits (like 10305), so I took off the "10" to match the room sensor file. |
| person_id | Text | none | The ID of the person who scanned. The first letter tells who it is: D = doctor, N = nurse, G = parent, P = patient. |
| person_type | Text (Doctor, Nurse, Parent or Patient) | none | The kind of person who scanned. |
| access_status | Text (Granted or Denied) | none | Whether the scan was let in. Every scan in my sample is Granted. |
| room_reading_time | Date and time (YYYY-MM-DD HH:MM, 24-hour) | none | The time of the room sensor reading I matched to the scan. I used the most recent reading at or before the scan (6:00 PM or 6:30 PM), because the sensor only reads every 30 minutes. |
| room_temp_f | Decimal number | degrees Fahrenheit (°F) | The room temperature at that room sensor reading. |
| door_status | Text (open or closed) | none | Whether the door was open or closed at that room sensor reading. |
| patient_id | Text | none | The ID of the patient whose vitals I attached. It is P1002 for scans in room 334 and blank for the other rooms. |
| vitals_reading_time | Date and time (YYYY-MM-DD HH:MM, 24-hour) | none | The time of the patient vitals reading I matched to the scan. I used the most recent one at or before the scan, because vitals are taken every 5 minutes. Blank if no patient is linked. |
| heart_rate_bpm | Whole number | beats per minute (bpm) | The patient's heart rate at that vitals reading. Blank if no patient is linked. |
| breathing_rate_per_min | Whole number | breaths per minute | The patient's breathing rate at that vitals reading. Blank if no patient is linked. |
| patient_temp_f | Decimal number | degrees Fahrenheit (°F) | The patient's body temperature at that vitals reading. Blank if no patient is linked. |
