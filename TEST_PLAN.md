# Manual verification plan (fictional data only)

Record expected and actual results; mark each PASS/FAIL after running the app. This is a software smoke-test plan, not clinical validation.

Accounts: patients `jordan`, `priya`, `sam` (password `patient123`); doctors `dr.patel`, `dr.ortega` (password `doctor123`).
Before you start, open the browser's developer tools console and keep it open. Use **Reset sample data** between sections if you want a clean start.

## A. Start-up

1. `npm install`, `npm run dev`: the app loads without a console error and shows the sign-in page, not the workspace.
2. `npm run build`: Vite produces a production bundle without errors.

## B. Sign-in

3. Patient is selected by default and three demo patient accounts are listed. Choose Doctor: two demo doctor accounts are listed.
4. Submit with empty fields: the browser blocks it.
5. Sign in as `jordan` with a wrong password: "Username or password is incorrect." and you stay on the sign-in page.
6. Enter `jordan` / `patient123` while **Doctor** is selected: you are refused with a message saying it is a patient account.
7. Enter `dr.patel` / `doctor123` while **Patient** is selected: you are refused with a message saying it is a doctor account.
8. Click **Fill in** on a demo account: username and password are filled. The password field is masked.
9. Sign in with ` JORDAN ` (capitals, spaces) and `patient123`: it works.
10. Sign in, refresh the page: you stay signed in. Sign out, refresh: you stay on the sign-in page.
11. The sign-in page says the app is demo only and not to enter real credentials or patient information.

## C. Patient (sign in as `jordan`)

12. The top bar shows "Patient view"; the greeting uses the first name; the menu has Overview, Recovery plan, HealthHistory Lens, SignalCare, Care team.
13. Overview: stats and task list render; Next check-in shows Oct 16 · 10:00 AM from the database.
14. Recovery plan shows only Jordan's four tasks, each with "Assigned by ..." and a due date where one is set. No Remove button. Priya's and Sam's tasks are not visible anywhere.
15. HealthHistory Lens shows only Jordan's records. Two "Blood pressure" records with different sources produce a candidate group that says **Review required**.
16. Search by record type, result and source. Searching does not hide the candidate group.
17. The comparison warning does not claim a clinical trend or interpretation.
18. Care team shows Dr. Maya Patel and Sarah Chen, RN only.
19. Add a task with a title only: SignalCare shows `OWNER_MISSING`, `DATE_MISSING` and `ACK_MISSING` for it, each with an explanation.
20. Add a task with an owner and a confirmed future date: no `OWNER_MISSING`, `DATE_MISSING` or `DATE_PASSED` for it.
21. Acknowledge the title-only task from SignalCare: `ACK_MISSING` clears but the task stays open.
22. Mark a task done: it no longer raises `ACK_MISSING`, and progress updates.
23. Add a task with a due date before today: `DATE_PASSED` appears. With a future date it does not.
24. Try an empty task name or a name of only spaces: the form shows a message and does not save.
25. Add a health record with a date and source; leave Source blank on another: it shows "Self-reported".
26. The SignalCare menu badge equals the number of findings on the SignalCare page.

## D. Doctor (sign in as `dr.patel`)

27. The top bar shows "Doctor view"; the greeting says "Dr. Patel"; the menu has Caseload, Patient plan, Patient history, SignalCare, My profile.
28. Caseload lists Jordan Davis and Priya Nair only. Sam Okafor does not appear anywhere.
29. Select Jordan: Patient plan opens with Jordan chosen. There are no completion checkboxes, and every task has a Remove button.
30. Use the patient selector to switch to Priya: the plan changes. The selector offers only Jordan and Priya.
31. Assign a task with owner and due date: it appears with "Assigned by Dr. Maya Patel" and "Not yet acknowledged".
32. Remove a task: first click asks for confirmation; Cancel keeps it; Confirm remove deletes it.
33. Schedule a check-in (date today or later): it is listed as Upcoming.
34. Patient history: add a clinical record with Source blank. The source shows "Dr. Maya Patel".
35. SignalCare lists findings grouped by Jordan and Priya, with rule IDs, and no Acknowledge button. "Open Priya's plan" jumps to her plan.
36. My profile lists what a doctor can and cannot do, and the assigned patients.
37. Export caseload report: the JSON contains two patients (tasks, history, SignalCare findings) and limitations, and nobody else's patients.
38. Sign in as `dr.ortega`: only Sam Okafor is visible, in the caseload, the selector and SignalCare.

## E. Both roles together (use the same browser)

39. After steps 31, 33 and 34, sign out and sign in as `jordan`: the assigned task, the clinical record and the new check-in are all visible.
40. As Jordan, the assigned task raises `ACK_MISSING`. Acknowledge it and mark it done.
41. Sign in as `dr.patel` again: the task shows as Completed in Jordan's plan.

## F. Export, reset, persistence

42. As a patient, export report: the JSON contains only that patient's tasks, history, SignalCare findings and limitations.
43. Refresh after changes: tasks, records and acknowledged status are still there.
44. Reset sample data, then Cancel: local changes remain. Reset again and confirm: the original sample data returns and you stay signed in.
45. Edit a value in `src/data/carebridge-db.json`, raise `meta.version`, reload: the change appears.

## G. Layout and console

46. At phone width (about 390 px) the sign-in page, every patient screen and every doctor screen has no sideways scrolling, and the menu opens and closes.
47. Check the browser console for runtime errors during all of the above.
