### Exercise 1 — Add the degree honors rule

Run this after defining `UNIVERSITY_KB`. It adds the rule only once and places it before the course rules.

```python
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

# Avoid adding the same rule more than once if you rerun this cell.
UNIVERSITY_KB["rules"] = [
    rule for rule in UNIVERSITY_KB["rules"]
    if rule["id"] != "RULE_07"
]
UNIVERSITY_KB["rules"].insert(0, degree_honors_rule)
```

### Exercise 2 — Replace `evaluate_student()` with a tracing version

This prints each rule condition as it is checked, and returns the result when a rule matches.

```python
def evaluate_student(student_record, kb):
    name = student_record.get("student_name", "Unknown")
    student_id = student_record.get("student_id", "Unknown")

    print(f"\n--- EVALUATING STUDENT: {name} (ID: {student_id}) ---")

    for rule in kb["rules"]:
        rule_id = rule["id"]
        condition_type = rule["condition_type"]
        conditions = rule["conditions"]

        if condition_type == "degree_honors":
            if "cgpa" not in student_record or "disciplinary_warnings" not in student_record:
                print(f"{rule_id}: skipped; CGPA or disciplinary warnings not provided.")
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

        elif condition_type == "attendance_below":
            if "attendance" not in student_record:
                print(f"{rule_id}: skipped; attendance not provided.")
                continue

            matches = student_record["attendance"] < 75
            print(
                f"{rule_id}: attendance ({student_record['attendance']}) < 75 "
                f"-> {matches}"
            )

        elif condition_type == "marks_range":
            if "total_marks" not in student_record:
                print(f"{rule_id}: skipped; total marks not provided.")
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
        "action": "Check the student record and Knowledge Base rules."
    }
```

### Test the honors rule

The values `3.85` and `0` below are examples; change them to your actual CGPA and disciplinary-warning count.

```python
degree_student = {
    "student_id": "YOUR-STUDENT-ID",
    "student_name": "Muhammad Arshad",
    "cgpa": 3.85,
    "disciplinary_warnings": 0
}

result = evaluate_student(degree_student, UNIVERSITY_KB)

print(f"Rule: {result['rule_id']}")
print(f"Status: {result['status']}")
print(f"Advisory Action: {result['action']}")
```
