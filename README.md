# Ejercicios sobre Bases de Datos (2023)

### Devolver la información: 

### - Nombre
### - Apellido
### -  N° de Departamento

### de los empleados que trabajan en los departamentos 9,10 y 11.

```sql
SELECT
    first_name,
    last_name,
    department_id
FROM
    employees
WHERE
    department_id IN ( 10, 9, 11 );
```

### Mostrar la cantidad de empleados que hay por cada nombre de departamento.

```sql
SELECT
    e.department_id,
    department_name,
    COUNT(*)
FROM
    employees e
    INNER JOIN departments d ON d.department_id = e.department_id
GROUP BY
    e.department_id,
    department_name;
```

### ¿Cuál fue el año donde ingresaron más trabajadores?

```sql
SELECT
    to_char(hire_date, 'YYYY') "Año Contratación",
    COUNT(employee_id)         "Empleados"
FROM
    hr.employees
GROUP BY
    to_char(hire_date, 'YYYY')
ORDER BY
    2 DESC

```
### Actualizar la fecha de contratación del empleado 104 cinco días después de la fecha almacenada.

```sql
UPDATE employees
SET
    hire_date = hire_date + 5
WHERE
    employee_id = 104
```

### Se necesita crear un procedimiento almacenado denominado simulator_salary que permita simular el cálculo del salario de un empleado para los 12 meses del próximo año. Para poder realizar la simulación debe tenerse en cuenta lo siguiente:
### -El primer, segundo y tercer mes el salario será el mismo que el de diciembre del año anterior.
### -Los meses de Abril, Mayo y Junio el salario será un 10% más que la del mes de diciembre del año anterior.
### -Los meses de Julio, Agosto, Septiembre, Octubre y Noviembre será un 30% más que el mes de diciembre del año anterior.
### Finalmente, en el último mes del año además del último incremento recibido en los meses de Julio, Agosto, Septiembre, Octubre y Noviembre tendrá un bono de 1000 dólares más.

```sql
CREATE OR REPLACE PROCEDURE simulator_salary(
    p_employee_id IN EMPLOYEES.EMPLOYEE_ID%TYPE
) IS
  v_salary EMPLOYEES.SALARY%TYPE;
  v_new_salary EMPLOYEES.SALARY%TYPE;
BEGIN
SELECT salary into v_salary FROM employees WHERE employee_id=p_employee_id;
DBMS_OUTPUT.PUT_LINE('SIMULADOR DE SALARIOS' || chr (10));
FOR counter IN 1..12 LOOP
 IF counter between 1 AND 3  THEN
   DBMS_OUTPUT.PUT_LINE(TO_CHAR(TO_DATE(counter, 'MM'), 'MONTH') || ' : ' || v_salary);
 ELSIF counter between 4 AND 6 THEN
   v_new_salary:= v_salary+v_salary * 0.01;
   DBMS_OUTPUT.PUT_LINE(TO_CHAR(TO_DATE(counter, 'MM'), 'MONTH') || ' : ' || v_new_salary);
 ELSIF counter between 7 AND 11 THEN
   v_new_salary:= v_salary+v_salary * 0.03;
   DBMS_OUTPUT.PUT_LINE(TO_CHAR(TO_DATE(counter, 'MM'), 'MONTH') || ' : ' || v_new_salary);
 ELSE
  v_new_salary:= (v_salary+v_salary * 0.03)+1000;
   DBMS_OUTPUT.PUT_LINE(TO_CHAR(TO_DATE(counter, 'MM'), 'MONTH') || ' : ' || v_new_salary);
 END IF;
END LOOP;
END;
```
### Realizar un stored procedure que liste los nombres y apellidos de los empleados que fueron contratados en un determinado año por un departamento dado 

```sql

CREATE OR REPLACE PROCEDURE emp_full_name( p_depto_name IN departments.department_name%TYPE,p_year IN NUMBER) IS
v_first_name employees.first_name%TYPE;
v_last_name employees.last_name%TYPE;
BEGIN
   For D in (SELECT e.first_name, e.last_name into v_first_name,v_last_name
               FROM employees e
               join departments d
                 on e.department_id = d.department_id 
              where d.department_name = p_depto_name
                and extract(year from e.hire_date) = p_year) 
   LOOP     
        DBMS_OUTPUT.PUT_LINE(D.FIRST_NAME || ' ' || D.LAST_NAME);
   END LOOP;
END;
```
### Realizar un procedimiento almacenado que devuelva el nombre, su puesto laboral y la fecha de contratación de un empleado cuyo id es pasado por parámetro en su invocación.

```sql

CREATE OR REPLACE PROCEDURE emp_sal_query 
(p_empnro IN 
employees.employee_id%TYPE)
IS
 r_emp employees%ROWTYPE;
BEGIN
 SELECT * into r_emp
 FROM employees WHERE employee_id = p_empnro;
 DBMS_OUTPUT.PUT_LINE('Employee # : ' || p_empnro);
 DBMS_OUTPUT.PUT_LINE('Name : ' || r_emp.last_name);
 DBMS_OUTPUT.PUT_LINE('Job : ' || r_emp.email);
 DBMS_OUTPUT.PUT_LINE('Hire Date : ' || r_emp.salary);
END;
```
### Realizar un procedimiento almacenado que permita actualizar el nombre de un departamento basado en un código.Deberá manejar excepciones para los casos que sean necesarios.

```sql

CREATE OR REPLACE PROCEDURE upd_department(p_new_name departments.department_name%TYPE,p_deptno departments.department_id%TYPE ) 
IS
e_invalid_dept EXCEPTION;
BEGIN
           UPDATE departments
           SET department_name = p_new_name
           WHERE department_id = p_deptno;
          IF SQL%NOTFOUND THEN
             RAISE e_invalid_dept;
          END IF;
          EXCEPTION
            WHEN e_invalid_dept THEN
            DBMS_OUTPUT.PUT_LINE ('No such department');
            DBMS_OUTPUT.PUT_LINE (SQLCODE);
           WHEN OTHERS THEN
           DBMS_OUTPUT.PUT_LINE('Otro Error -> '||SQLERRM);
 END;
```
### Realizar un procedimiento almacenado que permita insertar una región geográfica dado dos parámetros un id y un nombre de región. Deberá manejar excepciones para el caso de que se intente colocar un id que ya exista. Use transacciones.

```sql

CREATE OR REPLACE PROCEDURE insert_region(p_id regions.region_id%type,p_name regions.region_name%type) 
IS
v_total_rows number;
BEGIN
insert into regions values(p_id,p_name);
v_total_rows := sql%rowcount;
dbms_output.put_line(v_total_rows || ' Region insertada/s');
COMMIT;
EXCEPTION
WHEN dup_val_on_index THEN
dbms_output.put_line('Clave duplicada no se puede insertar la region');
END;
```
### Realizar un procedimiento almacenado que permita almacenar un empleado completo. El SP deberá devolver la cantidad de filas afectadas por la inserción deberá ser devuelta por el atributo SQL%ROWCOUNT. Además, el procedimiento deberá realizar lo siguiente:
Calcular el id del nuevo empleado calculando el máximo valor existente y sumándole uno.
### -Los nombres, apellidos, email y teléfono de contacto.
### -El id del puesto a ingresar debe existir en la tabla jobs.
### -El id del departamento a ingresar debe existir en la tabla departments.
### -La fecha de contratación es la fecha actual del sistema.
### -El salario mínimo será de 7000.
### -Deberá colocar las excepciones necesarias para la inserción correcta del empleado.

```sql

CREATE OR REPLACE PROCEDURE new_employee (p_first_name in employees.first_name%TYPE, 
p_last_name in employees.last_name%TYPE,
p_email in employees.email%TYPE,
p_phone_number in employees.phone_number%TYPE,
p_job_id in employees.job_id%TYPE,
p_manager_id in employees.manager_id%TYPE,
p_department_id in employees.department_id%TYPE,
p_dni in employees.dni%TYPE)
is
v_new_id number;
v_total_rows number;
v_new_jobs employees.job_id%TYPE;
v_new_department_id employees.department_id%TYPE;
c_salary CONSTANT employees.salary%TYPE:=7000;
BEGIN
select max(employee_id) into v_new_id FROM employees;
v_new_id:=v_new_id+1;
select job_id  into v_new_jobs from jobs where job_id=p_job_id;
select department_id  into v_new_department_id from departments where department_id=p_department_id;
insert into employees values(v_new_id,p_first_name,p_last_name,p_email,p_phone_number,sysdate(),p_job_id,c_salary,p_manager_id,p_department_id,p_dni);
v_total_rows := sql%rowcount;
COMMIT;
dbms_output.put_line('Filas Afectadas '|| v_total_rows);
EXCEPTION
WHEN no_data_found THEN
dbms_output.put_line('No EXISTE dicho JOB/DEPARTMENT');
WHEN others THEN
dbms_output.put_line('Otro Error -> '||SQLERRM);
END;
```

