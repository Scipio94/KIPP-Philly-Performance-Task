~~~ SQL
CREATE TEMP TABLE t1 AS -- Creating TEMP TABLE for Calculation
SELECT 
  DISTINCT a.StudentID,
  E.SCHOOLID,
  e.GRADE_LEVEL,
  a.EventDate,
  a.AttendanceCode,
  CASE -- If THEN based on attendance code
    /*
     P = Present
    EA = Excused Absence 
    ET = Excused Tardy
    UA = Unexcused Absence
    UT = Unexcused Tardy
    S = Suspended
    N/A = No attendance entered
    
    Any of the codes UA, EA, N/A, or S shall beconsidered absent
    */
    WHEN AttendanceCode IN ('P','ET','UT') THEN 1 -- 1 Present
    WHEN AttendanceCode NOT IN ('P','ET','UT') THEN 0 -- 0 Absent
    END AS ADA_Calc
FROM `single-being-353600.KIPP_Philly_Performance_Task.KIPP_Philly_Attendance_14_15` AS a 
INNER JOIN `single-being-353600.KIPP_Philly_Performance_Task.Current_Students` AS e 
 ON a.StudentID = e.ID;

/*Fining ADA By School and Grade Level*/
SELECT 
  DISTINCT t1.SCHOOLID,
  t1.GRADE_LEVEL,
  ROUND(AVG(t1.ADA_Calc),2) AS ADA
FROM t1
GROUP BY t1.SCHOOLID,t1.GRADE_LEVEL
ORDER BY t1.GRADE_LEVEL
~~~
