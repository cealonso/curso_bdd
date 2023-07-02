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


