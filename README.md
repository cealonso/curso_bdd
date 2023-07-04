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

