Use these as replacements/additions in your Colab notebook. Run the Knowledge Base cell first, then the inference-engine cell, then the test cell.

### Exercise 1 — Add the degree honors rule

Add this rule to `UNIVERSITY_KB["rules"]`. The check below replaces an existing `RULE_07` if you rerun the cell, so it won’t add duplicates.

```python name=degree_honors_rule.py
degree_honors_rule = {
    "id": "RULE_07",
    "condition_type": "degree_honors",
    "conditions": {
        "cgpa_min": 3.70,
        "disciplinary_warnings": 0
    },
    "status": "Graduated with Magna Cum Laude Honors",
    "action": "Recognize the student's academic achievement at graduation."
}

UNIVERSITY_KB["rules"] = [
    rule for rule in UNIVERSITY_KB["rules"]
    if rule["id"] != "RULE_07"
]
UNIVERSITY_KB["rules"].append(degree_honors_rule)
```

### Exercise 2 — Trace each rule as it is evaluated

Replace your existing `evaluate_student()` with this version. It prints each rule’s conditions and whether they match. Honors records can be evaluated with CGPA and disciplinary warnings; course records use attendance and marks.

```python name=evaluate_student.py
def evaluate_student(student_record, kb):
    name = student_record.get("student_name", "Unknown")
    student_id = student_record.get("student_id", "Unknown")

    print(f"\n--- EVALUATING STUDENT: {name} (ID: {student_id}) ---")

    for rule in kb["rules"]:
        rule_id = rule["id"]
        condition_type = rule["condition_type"]
        conditions = rule["conditions"]

        if condition_type == "attendance_below":
            if "attendance" not in student_record:
                print(f"{rule_id}: skipped; attendance was not provided.")
                continue

            matches = student_record["attendance"] < 75
            print(
                f"{rule_id}: attendance ({student_record['attendance']}) < 75 "
                f"-> {matches}"
            )

        elif condition_type == "degree_honors":
            required = ("cgpa", "disciplinary_warnings")
            if not all(field in student_record for field in required):
                print(
                    f"{rule_id}: skipped; CGPA or disciplinary warnings "
                    "were not provided."
                )
                continue

            cgpa_matches = student_record["cgpa"] >= conditions["cgpa_min"]
            warnings_match = (
                student_record["disciplinary_warnings"]
                == conditions["disciplinary_warnings"]
            )
            matches = cgpa_matches and warnings_match

            print(
                f"{rule_id}: cgpa ({student_record['cgpa']}) >= "
                f"{conditions['cgpa_min']} AND disciplinary_warnings "
                f"({student_record['disciplinary_warnings']}) == "
                f"{conditions['disciplinary_warnings']} -> {matches}"
            )

        elif condition_type == "marks_range":
            if "total_marks" not in student_record:
                print(f"{rule_id}: skipped; total_marks was not provided.")
                continue

            marks = student_record["total_marks"]
            matches = conditions["marks_min"] <= marks <= conditions["marks_max"]
            print(
                f"{rule_id}: {conditions['marks_min']} <= marks ({marks}) "
                f"<= {conditions['marks_max']} -> {matches}"
            )

        else:
            print(f"{rule_id}: skipped; unknown condition type.")
            continue

        if matches:
            return {
                "rule_id": rule_id,
                "grade": rule.get("grade", "N/A"),
                "status": rule["status"],
                "action": rule["action"]
            }

    return {
        "rule_id": "UNKNOWN",
        "grade": "N/A",
        "status": "No matching rule",
        "action": "Check the student record and the Knowledge Base rules."
    }
```

### Test the honors rule

```python name=honors_test.py
degree_student = {
    "student_id": "BSCS-2024-099",
    "student_name": "Muhammad Asrhad",
    "cgpa": 3.85,
    "disciplinary_warnings": 0
}

result = evaluate_student(degree_student, UNIVERSITY_KB)

print(f"Rule: {result['rule_id']}")
print(f"Status: {result['status']}")
print(f"Advisory Action: {result['action']}")
```

The trace shows each rule check. The honors result is returned when CGPA is at least `3.70` and disciplinary warnings equal `0`.
