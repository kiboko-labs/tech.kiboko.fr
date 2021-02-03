Requêtes SQL utiles
===

### Fonction Postgres pour décoder les champs `serialized_data`

Avant toute chose, nous devons créer les fonctions nécessaires à ce décodage et qui seront réutilisables dans nos requêtes SQL :

Référence : https://gist.github.com/storeman/cecb10bab249f43562cddabc1d9dd7c9

```sql
CREATE OR REPLACE FUNCTION php_unserialize(str text)
    RETURNS json AS
$BODY$
DECLARE
    vartype CHAR;
    varlength INT;
    jsonstr TEXT;
    varcount INT;
    jsonval JSONB;
    arrkey JSON;
    arrval JSON;
    -- String length of the serialized data
    serialized_string_length INT;
BEGIN
    CASE substring(str, 1, 1)
        WHEN 'a' THEN -- array
        -- init object
            jsonval := '{}'::jsonb;

            -- remove the "a" and ":" characters
            str := substring(str, 3);

            -- Detect number of values in array
            varlength := substring(str, 1, position(':' IN str) - 1)::INT;

            -- Base size of array is 5 (a:[size]:{})
            serialized_string_length := 5 + char_length(varlength::TEXT);

            -- If no values, return empty object, as this always returns objects
            IF varlength = 0 THEN
                return json_build_array('array', jsonval, serialized_var_size)::JSON;
            END IF;

            -- remove the array size and ":{"
            str := substring(str, char_length(varlength::TEXT) + 3);

            -- Find the number of variables specified
            FOR varcount IN 1 .. varlength LOOP
                    -- Find the value of the key and remove it from base string
                    arrkey := php_unserialize(str);
                    str := substring(str, (arrkey->>2)::INT + 1);

                    -- Find the value of the value and remove it from base string
                    arrval := php_unserialize(str);
                    str := substring(str, (arrval->>2)::INT + 1);

                    serialized_string_length := serialized_string_length + (arrkey->>2)::INT + (arrval->>2)::INT;

                    -- Append value
                    jsonval := jsonval || jsonb_build_object(arrkey->>1, arrval->1);
                END LOOP;

            return json_build_array('array', jsonval, serialized_string_length);
        WHEN 'b' THEN -- boolean
        return json_build_array('bool',(CASE substring(str, 3, 1) WHEN '1' THEN TRUE ELSE FALSE END)::TEXT, 4);
        WHEN 'd' THEN -- float
        return json_build_array('float', substring(str, 3, position(';' IN str) - 3)::TEXT, position(';' IN str));
        WHEN 'i' THEN -- int
        return json_build_array('int', substring(str, 3, position(';' IN str) - 3)::TEXT, position(';' IN str));
        WHEN 'N' THEN -- null
        return json_build_array('null', 'null'::TEXT, 2);
        WHEN 's' THEN -- string
        varlength := substring(str, 3, position(':' IN substring(str, 3)) - 1)::INT;
        return json_build_array('string', substring(str, char_length(varlength::TEXT) + 5, varlength)::TEXT, position(';' IN str));
        ELSE
            RAISE EXCEPTION 'Unable to decode serialized value, unsupported type: %', substr(str, 1, 1);
        END CASE;
END;
$BODY$
    LANGUAGE plpgsql IMMUTABLE
                     COST 10;

---
--- The main function
---
CREATE OR REPLACE FUNCTION php_unserialize_to_json(str text)
    RETURNS json AS
$BODY$
DECLARE
    varlength INT;
BEGIN
    CASE substring(str, 1, 1)
        WHEN 'a' THEN
            return php_unserialize(str)->1;
        WHEN 'b' THEN
            return php_unserialize(str)->1;
        WHEN 'd' THEN
            return php_unserialize(str)->1;
        WHEN 'i' THEN
            return php_unserialize(str)->1;
        WHEN 'N' THEN
            return php_unserialize(str)->1;
        WHEN 's' THEN
            return php_unserialize(str)->1;
        ELSE
            RETURN NULL;
        END CASE;
END;
$BODY$
    LANGUAGE plpgsql IMMUTABLE
                     COST 10;
```

Puis, dans toute requête SQL, utiliser la fonction telle que fait sur la 3e colonne de la requête suivante :

```SQL

SELECT
       product.id,
       product.sku,
       php_unserialize_to_json(convert_from(decode(product.serialized_data, 'base64'), 'UTF8'))
FROM oro_product AS product

```

Les opérateurs JSON standard peuvent être utilisés sur cette colonne, comme par exemple :

```SQL

SELECT
       product.id,
       product.sku,
       php_unserialize_to_json(convert_from(decode(product.serialized_data, 'base64'), 'UTF8')) ->> 'ak_date_peremption'
FROM oro_product AS product
```

https://www.postgresqltutorial.com/postgresql-json/
