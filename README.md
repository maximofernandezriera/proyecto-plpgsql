# Pl/Pgsql en un minuto

PL/pgSQL es un lenguaje de programación procedural de código abierto, diseñado para extender la funcionalidad del motor de bases de datos PostgreSQL. PL/pgSQL se utiliza para escribir procedimientos almacenados, funciones y disparadores en PostgreSQL.

Un procedimiento almacenado es un bloque de código que se almacena en el servidor de bases de datos y se puede llamar desde cualquier parte del programa. Estos bloques de código pueden ser utilizados para implementar lógica empresarial, realizar cálculos complejos o actualizar datos en la base de datos.

PL/pgSQL es un lenguaje estructurado similar a otros lenguajes de programación, con elementos como variables, estructuras de control de flujo (if/else, while, for), funciones, y operadores.

Para utilizar PL/pgSQL, es necesario tener acceso a una base de datos PostgreSQL y tener los permisos necesarios para crear procedimientos almacenados o funciones.

Una función PL/pgSQL que devuelve la suma de dos números:

        CREATE OR REPLACE FUNCTION sumar(a INTEGER, b INTEGER)
        RETURNS INTEGER AS $$
        BEGIN
          RETURN a + b;
        END;
        $$ LANGUAGE plpgsql;

La función se define utilizando la sintaxis PL/pgSQL, que incluye una declaración BEGIN para indicar el inicio del bloque de código y una declaración RETURN para devolver el resultado.

# Un mini proyecto


Crea una base de datos para almacenar información sobre tus amigos y sus cumpleaños. Utiliza PL/pgSQL para escribir una función que te permita agregar nuevos amigos y otra función que te muestre cuántos días faltan para el próximo cumpleaños de cada amigo.

# Un vistazo al código

        -- Primero, creamos la tabla "amigos" con las columnas "nombre" y "fecha_de_nacimiento"
        CREATE TABLE amigos (
            nombre TEXT,
            fecha_de_nacimiento DATE
        );

        -- Luego, creamos la función "agregar_amigo" para insertar nuevos amigos en la tabla
        CREATE OR REPLACE FUNCTION agregar_amigo(nombre_amigo TEXT, fecha_nacimiento DATE)
        RETURNS VOID AS $$
        BEGIN
            INSERT INTO amigos (nombre, fecha_de_nacimiento) VALUES (nombre_amigo, fecha_nacimiento);
        END;
        $$ LANGUAGE plpgsql;

        -- Finalmente, creamos la función "dias_para_cumpleaños" que calcula cuántos días faltan para el próximo cumpleaños de cada amigo
        CREATE OR REPLACE FUNCTION dias_para_cumpleaños()
        RETURNS TABLE (nombre TEXT, dias INTEGER) AS $$
        DECLARE
            hoy DATE := CURRENT_DATE;
        BEGIN
            RETURN QUERY SELECT nombre,
                EXTRACT(DAY FROM ((fecha_de_nacimiento + ((EXTRACT(YEAR FROM hoy) - EXTRACT(YEAR FROM fecha_de_nacimiento)) || ' years')::INTERVAL) -                     hoy))::INTEGER
                FROM amigos;
        END;
        $$ LANGUAGE plpgsql;

# Una explicación general

Este código crea una tabla llamada “amigos” con dos columnas: “nombre” y “fecha_de_nacimiento”. También define dos funciones: agregar_amigo que permite insertar nuevos registros en la tabla amigos, y dias_para_cumpleaños que calcula cuántos días faltan para el próximo cumpleaños de cada amigo.

# La función dias_para_cumpleaños

La función dias_para_cumpleaños calcula cuántos días faltan para el próximo cumpleaños de cada amigo. Para hacer esto, primero declara una variable hoy que almacena la fecha actual. Luego, utiliza una consulta SELECT para seleccionar el nombre y la fecha de nacimiento de cada amigo en la tabla amigos.

Para calcular cuántos días faltan para el próximo cumpleaños de cada amigo, la función utiliza la función EXTRACT para extraer el año de la fecha actual y restarlo del año de nacimiento del amigo. Luego, agrega esa cantidad de años a la fecha de nacimiento del amigo para obtener su próximo cumpleaños. Finalmente, resta esa fecha del cumpleaños del amigo de la fecha actual y extrae el número de días entre ambas fechas.

# La función agregar_amigo

La función agregar_amigo permite insertar nuevos registros en la tabla amigos. Esta función toma dos argumentos: nombre_amigo y fecha_nacimiento, que representan el nombre y la fecha de nacimiento del amigo que se quiere agregar.

Dentro de la función, se utiliza el comando INSERT INTO para insertar un nuevo registro en la tabla amigos. Los valores de las columnas “nombre” y “fecha_de_nacimiento” se establecen con los valores de los argumentos nombre_amigo y fecha_nacimiento, respectivamente.

Por ejemplo, si quisieras agregar a un amigo llamado “Juan” cuya fecha de nacimiento es el 1 de enero de 2008, podrías llamar a la función así: SELECT agregar_amigo('Juan', '2008-01-01');


# El reto

Implementa y explota este miniproyecto en el entorno que prefieras.
