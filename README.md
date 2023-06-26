# Ejercicios sobre Bases de Datos (2023)

### Devolver la información: Nombre, Apellido y N° de Departamento de los empleados que trabajan en los departamentos 9,10 y 11.

```sql
SELECT FIRST_NAME, LAST_NAME, DEPARTMENT_ID
FROM EMPLOYEES
WHERE DEPARTMENT_ID IN (10, 9, 11);
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


