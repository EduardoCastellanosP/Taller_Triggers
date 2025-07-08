# 🚀 **Taller de Triggers en MySQL**



### Caso 1: Control de Stock de Productos

```sql

DELIMITER //

CREATE TRIGGER before_control_stock
BEFORE INSERT ON ventas
FOR EACH ROW
BEGIN 
    DECLARE stock_actual INT;

    
    SELECT stock INTO stock_actual
    FROM productos
    WHERE id = NEW.id_producto;

 
    IF NEW.cantidad > stock_actual THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Error: No hay suficiente stock para esta venta.';
    END IF;
END//

DELIMITER ;

    
```

```sql
INSERT INTO ventas (id_producto, cantidad) VALUES (2, 10) ;
ERROR 1644 (45000): Error: No hay suficiente stock para esta venta.

```

<img src="/home/camper/.config/Typora/typora-user-images/image-20250708121820069.png" alt="image-20250708121820069" style="zoom:150%;" />



### **🔹 Caso 2: Registro Automático de Cambios en Salarios**



```sql
DELIMITER// 

CREATE TRIGGER registro_automatico_salarios
BEFORE UPDATE ON empleados
FOR EACH ROW
BEGIN 

	INSERT INTO historial_salarios ( id_empleado,salario_anterior,salario_nuevo)
	VALUES (OLD.id, OLD.salario,NEW.salario);
END//

DELIMITER ;
```



```sql
UPDATE empleados SET salario = 1700.00 WHERE id = 1;

```

![image-20250708123556270](/home/camper/.config/Typora/typora-user-images/image-20250708123556270.png)



### **🔹 Caso 3: Registro de Eliminaciones en Auditoría**





```sql
DELIMITER //

CREATE TRIGGER after_delete_auditorias
AFTER DELETE ON clientes
FOR EACH ROW
BEGIN
    INSERT INTO clientes_auditoria(id_cliente, nombre, email, fecha_eliminacion)
    VALUES (OLD.id, OLD.nombre, OLD.email, NOW());
END//

DELIMITER ;

	
	
```

![image-20250708124821994](/home/camper/.config/Typora/typora-user-images/image-20250708124821994.png)





### **🔹 Caso 4: Restricción de Eliminación de Pedidos Pendientes**

```sql
CREATE TRIGGER bloquear_eliminacion_pedidos_pendientes
BEFORE DELETE ON pedidos
FOR EACH ROW
BEGIN
    IF OLD.estado = 'pendiente' THEN
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Error: No se puede eliminar un pedido pendiente.';
    END IF;
END//

DELIMITER ;
```



![image-20250708125007934](/home/camper/.config/Typora/typora-user-images/image-20250708125007934.png)

![image-20250708125139853](/home/camper/.config/Typora/typora-user-images/image-20250708125139853.png)