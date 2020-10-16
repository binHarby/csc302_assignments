# Database Managment Systems Assignment 2

---
## TABLES OF THE ERD 🚀️📜
---

#### we create them in this order and code

```sql
--Student table
CREATE TABLE STUDENT(
    STU_ID  NUMBER,
    STU_FNAME   VARCHAR2(50) NOT NULL,
    STU_LNAME   VARCHAR2(50) NOT NULL,
    CONSTRAINT STUDENT_PK PRIMARY KEY (STU_ID)
);
--Location table (LOCATION IS A KEYWORD SO '_' WAS ADDED TO MAKE IT CLEAR)
CREATE TABLE LOCATION_ (
    LOC_CODE    VARCHAR2(10),
    LOC_NAME    VARCHAR2(50) NOT NULL,
    LOC_COUNTRY VARCHAR2(50)NOT NULL,
    CONSTRAINT LOCATION__PK PRIMARY KEY (LOC_CODE)
);
-- Department table
CREATE TABLE DEPARTMENT (
    DEP_CODE    VARCHAR2(10),
    DEP_NAME    VARCHAR2(50) NOT NULL,
    CONSTRAINT DEPARTMENT_PK PRIMARY KEY (DEP_CODE)
);
-- Instructor table

CREATE TABLE INSTRUCTOR(
    INS_ID  NUMBER,
    INS_FNAME   VARCHAR2(50) NOT NULL,
    INS_LNAME   VARCHAR2(50) NOT NULL,
    DEP_CODE      VARCHAR2(10),
    CONSTRAINT INSTRUCTOR_PK PRIMARY KEY (INS_ID),
    CONSTRAINT DEPARTMENT_INSTRUCTOR_FK FOREIGN KEY (DEP_CODE)
    REFERENCES DEPARTMENT (DEP_CODE)
);
-- Course table
CREATE TABLE COURSE (
    CRS_CODE    VARCHAR2(10),
    CRS_TITLE   VARCHAR2(50) NOT NULL,
    CRS_CREDITS NUMBER(1) NOT NULL,
    DEP_CODE   VARCHAR2(10) NOT NULL,
    CRS_DESCRIPTION LONG,
    CONSTRAINT COURSE_PK PRIMARY KEY (CRS_CODE),
    CONSTRAINT DEPARTMENT_COURSE_FK FOREIGN KEY (DEP_CODE)
    REFERENCES DEPARTMENT (DEP_CODE)
);
-- Qualified table
CREATE TABLE QUALIFIED (
    INS_ID  NUMBER,
    CRS_CODE    VARCHAR2(10) ,
    CONSTRAINT INSTRUCTOR_QUALIFIED_FK FOREIGN KEY (INS_ID)
    REFERENCES INSTRUCTOR (INS_ID),
    CONSTRAINT COURSE_QUALIFIED_FK FOREIGN KEY (CRS_CODE)
    REFERENCES COURSE (CRS_CODE)
);
--prerequisite table
CREATE TABLE PREREQUISITE (
    CRS_CODE        VARCHAR(10) NOT NULL,
    CRS_REQUIRES    VARCHAR2(50),
    CONSTRAINT PREREQUISITE_COURSE_PK FOREIGN KEY (CRS_CODE)
    REFERENCES COURSE (CRS_CODE)
);
--Section table
CREATE TABLE SECTION (
    SEC_ID      NUMBER,
    SEC_TERM    VARCHAR(16) NOT NULL,
    SEC_BLDG    VARCHAR(16) NOT NULL,
    SEC_ROOM    VARCHAR(16) ,
    SEC_TIME    INTERVAL DAY TO SECOND,
    CRS_CODE    VARCHAR2(10),
    LOC_CODE    VARCHAR2(10),
    INS_ID      NUMBER,

    CONSTRAINT SECTION_PK PRIMARY KEY (SEC_ID),
    CONSTRAINT SECTION_LOCATION__FK FOREIGN KEY (LOC_CODE) REFERENCES LOCATION_ (LOC_CODE),
    CONSTRAINT SECTION_COURSE_FK FOREIGN KEY (CRS_CODE) REFERENCES COURSE (CRS_CODE),
    CONSTRAINT SECTION_INSTRUCTOR_FK FOREIGN KEY (INS_ID) REFERENCES INSTRUCTOR (INS_ID)

);
--Enrollment table
CREATE TABLE ENROLLMENT (
   STU_ID   NUMBER,
   SEC_ID   NUMBER,
   GRADE_CODE   VARCHAR2(10),
   CONSTRAINT ENROLLMENT_STUDENT_FK FOREIGN KEY (STU_ID) REFERENCES STUDENT (STU_ID),
   CONSTRAINT ENROLLMENT_SECTION_FK FOREIGN KEY (SEC_ID) REFERENCES SECTION (SEC_ID)
);
```
---
## Analysis and Answering Questions  📈🗝️
---
### 1. Listing all my courses of the last term

```sql
SELECT COURSE.CRS_TITILE
FROM COURSE
JOIN SECTION ON COURSE.CRS_CODE=SECTION.CRS_CODE
AND SECTION.SEC_TERM="SPRING_2019-2020"
JOIN ENROLLMENT ON ENROLLMENT.SEC_ID=SECTION.SEC_ID
AND ENROLLMENT.STU_ID=1070401;
```

### 2. List the instructors’names of these courses in descending order

```sql
-- Selecting all the instructors
SELECT Instructor.ins_fname,
Instructor.ins_lname
FROM Instructor
-- Joining the instructors with the sections
JOIN Section
ON Instructor.ins_id = Section.ins_id
-- Pasting the previous
AND Section.crs_code IN (
        SELECT Course.crs_code
        FROM Course
        -- Joining the courses with the section on crs_code
        JOIN Section
        ON Course.crs_code = Section.crs_code
        -- Finding the largest number of the last term
        AND Section.sec_term = "SPRING_2019-2020"
        FROM Section)
        -- Joining Enrollment with the joint of courses and sections on sec_id
        JOIN Enrollment ON Enrollment.sec_id = Section.sec_id
        -- Checking if courses are mine
        AND Enrollment.stu_id = 1070401)
-- Sorting the names of the instructors in descending order
ORDER BY Instructor.ins_fname
DESC, Instructor.ins_lname DESC;
```

### 3. List all courses of the last term that have no prerequisite

```sql
SELECT Course.crs_title
FROM Course JOIN Section
ON Section.crs_code=Course.crs_code
-- Finding the largest number of the last term
AND Section.sec_term ="SPRING_2019-2020"
-- Finding whether the course has no prerequisite
AND Section.crs_code IN (
            SELECT Prerequisite. crs_code
            FROM Prerequisite
            WHERE Prerequisite. crs_requires IS NULL);
```

### 4. List all courses of the last term with grade above 'C'

```sql
SELECT Course.crs_title
From Course
-- Joining the courses with the section on crs_code
JOIN Section
ON Course.crs_code = Section.crs_code
-- Finding the largest number of the last term
AND Section.sec_term ="SPRING_2019-2020"
-- Joining Enrollment with the joint of courses and sections on sec_id
JOIN Enrollment
ON Enrollment.sec_id = Section.sec_id
-- Checking if courses are mine
AND Enrollment.stu_id = 1070401
-- Checking if grades arebigger than C
AND Enrollment.grade_code IN
(‘A+’, ‘A’, ‘A-’, ‘B+’, ‘B’, ‘B-’, ‘C+’);
```

### 5. Determine the CSIT department of the last term course

```sql
SELECT crs_title
FROM (
        -- Selecting all the courses
        SELECT *
        FROM Course
        -- Joining the courses with the section on crs_code
        JOIN Section
        ON Course.crs_code = Section.crs_code
        -- Join them on the term also
        AND Section.sec_term ="SPRING_2019-2020"
)
JOIN Department
ON Course.dep_code = Department.dep_code
AND Department.dep_name LIKE  '%CSIT%';
```

### 6. Determine the CSIT department of the last term course

```sql
SELECT crs_title
FROM (
        -- Selecting all the courses
        SELECT *
        FROM Course
        -- Joining the courses with the section on crs_code
        JOIN Section ON Course.crs_code = Section.crs_code
        -- Finding the largest number of the last term
        AND Section.sec_term = "SPRING_2019-2020" )WHERE crs_code IN (
                SELECT crs_code
                FROM Section
                -- Counting whether crs_code has more than 1 occurrences
                GROUP BY crs_code
                HAVING COUNT(*)>1
);
```

### 7. Determine the CSIT department of the last term course

```sql
SELECT sec_id, crs_code
FROM (
    SELECT *
    FROM (
        SELECT *
        FROM Course
        JOIN Section ON Course.crs_code = Section.crs_code
        AND Section.sec_term = "SPRING_2019-2020")

    WHERE crs_code IN (
        SELECT crs_code
        FROM Section
        GROUP BY crs_code
        HAVING COUNT(*)>1
))
JOIN Location ON Location.loc_code =Section.loc_code
AND loc_name == ‘Abu Dhabi’
OR loc_name == ‘Al-Ain’);
```

