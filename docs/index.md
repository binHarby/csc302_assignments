# DBMS Assignment 4

>Abdulla Alameri
>1070401

> Administered by Dr. Adel Khelifi

## Question #1
```sql
SELECT Teacher.FirstName, Teacher.LastName, Teacher.DepartmentID
FROM Teacher
JOIN Section
ON Section.TeacherID = Teacher.TeacherID
AND Section.SectionID in (
	SELECT SectionID
	FROM Enrollment
	WHERE StudentID = 1070401

);


```
## Question #2
```sql
SELECT Teacher.FirstName, Teacher.LastName
FROM (
	SELECT *
	FROM Teacher
	JOIN Section
	ON Section.TeacherID = Teacher.TeacherID
	AND Section.SectionID in (
		SELECT SectionID
		FROM Enrollment
		WHERE StudentID = 1070401
		)
)
WHERE Teacher.Rank LIKE ‘%TA%’;

```

## Question #3
```sql
SELECT CourseID
FROM Course
WHERE Type LIKE‘%offline%’ OR Type LIKE ’%online%’;


```
## Question #4
```sql
SELECT Course.Name, Enrollment.FinalGrade
FROM Enrollment
JOIN Section
ON Section.SectionID = Enrollment.SectionID
JOIN Course
ON Course.CourseID = Section.CourseID
AND Enrollment.StudentID = 1070401;

```
## Question #5
```sql
SELECT Day, StartTime, EndTime, Section.Room
FROM Schedule
JOIN Section
ON Schedule.ScheduleID = Section. SectionID
JOIN Enrollment
ON Section.SectionID = Enrollment.SectionID
AND Enrollment.StudentID = 1070401;

```
## Question #6
```sql
SELECT Schedule.Day, Schedule.StartTime, Schedule.EndTime, Section.Room, Teacher.LastName, Teacher.FirstName
FROM Schedule
JOIN Section
ON Section.ScheduleID = Schedule.ScheduleID
JOIN Teacher
ON Section.TeacherID = Teacher.TeacherID
JOIN Enrollment
ON Enrollment.SectionID = Section.SectionID
AND Enrollment.StudentID = 1070401;


```
## Question #7
```sql
SELECT Name
FROM Courses
JOIN Section
ON Section.CourseID = Course.CourseID
JOIN Enrollment
ON Enrollment.SectionID = Section.SectionID
AND Enrollment.StudentID = 1070401;


```
## Question #8
```sql
SELECT SectionID, Course.Name
FROM Section
JOIN Course
ON Course.CourseID = Section.CourseID
JOIN Enrollment
ON Enrollment.SectionID = Section.SectionID
AND Enrollment.StudentID = 1070401;

```

## Question #9
```sql
SELECT Teacher.FirstName, Teacher.LastName
FROM Teacher
JOIN Section
ON Section.TeacherID = Teacher.TeacherID
AND Section.SectionID IN (
	SELECT SectionID
	FROM Enrollment
	WHERE StudentID = 1070401

)
AND Teacher.TeacherID IN (
	SELECT TeacherID
	FROM Teacher
	GROUP BY TeacherID
	HAVING
		COUNT(TeacherID) > 1
	);

```

## Question #10
```sql
SELECT Section.SectionID
FROM Section
JOIN Schedule
ON Schedule.ScheduleID = Section.ScheduleID
JOIN Enrollment
ON Enrollment.SectionID = Section.SectionID
AND Enrollment.StudentID = 1070401
AND Schedule.ScheduleID IN (
	SELECT ScheduleID
	FROM Schedule
	GROUP BY CONCAT (Day, StartTime, EndTime)
	HAVING COUNT (CONCAT (Day, StartTime, EndTime)) > 1);

```
## Question #11
```sql
SELECT Name
FROM Course
WHERE CourseID IN (
	SELECT CourseID
	FROM Section
	WHERE SectionID IN (
		SELECT SectionID
		FROM Enrollment
		WHERE StudentID != 1070401));

```

## Question #12
```sql
SELECT Name, Description
FROM Course
JOIN Section
ON Section.CourseID = Course.CourseID
JOIN Enrollment
ON Enrollment.SectionID = Section.SectionID
AND Enrollment.StudentID = 1070401
AND Section.CourseID IN (
	SELECT Section.CourseID
	FROM Section
	GROUP BY Section.CourseID
	HAVING COUNT (Section.CourseID) > =1);

```

## Question #13
```sql
SELECT Teacher.FirstName, Teacher.LastName
FROM Teacher
JOIN Section
ON Section.TeacherID = Teacher.TeacherID
JOIN Enrollment
ON Enrollment.SectionID = Section.SectionID
AND Enrollment.StudentID = 1070401;

```
## Question #14
```sql
SELECT Course.Name, Section.SectionID
FROM Course
JOIN Section
ON Section.CourseID = Course.CourseID
JOIN Enrollment
ON Enrollment.SectionID = Section.SectionID
AND Enrollment.StudentID = 1070401
AND CourseID IN (
	SELECT CourseID
	FROM Course
	WHERE Course.Semester = (SELECT MAX (Course.Semester) FROM Course);

```
## Question #15
```sql
SELECT Section.SectionID
FROM Section S1, Section S2, Schedule Sch1, Schedule Sch2
WHERE S1.Room = S2.Room
AND S1.SectionID != S2.SectionID
AND SCH1.ScheduleID = SCH2.ScheduleID
AND CONCAT (SCH1.Day, SCH1.StartTime, SCH2.EndTime) = CONCAT (SCH2.Day, SCH2.StartTime, SCH2.EndTime;

```
