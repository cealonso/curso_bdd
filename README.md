# Ejercicios sobre Bases de Datos (2023)

### ¿Cuál fue el año donde ingresaron más trabajadores?

```
SELECT
    to_char(hire_date, 'YYYY') "Año Contratación",
    COUNT(employee_id)         "Empleados"
FROM
    hr.employees
GROUP BY
    to_char(hire_date, 'YYYY')
ORDER BY
    2 DESC


### Actualizar la fecha de contratación del empleado 104 cinco días después de la fecha almacenada.

```
UPDATE employees
SET
    hire_date = hire_date + 5
WHERE
    employee_id = 104



