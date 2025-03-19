# MiniBigDataExperiment
Un experimento para probar el mundo del Big Data

En este experimento se han generado datos sobre un supuesto estudio de tatuaje. Estos datos están almacenados en un csv de 100 000 lineas.

### Generación de datos
Se han generado los datos con una funcion de python usando una mezcla de Faker y Random. Con random se han dado valores de peso para que a la hora de hacer un análisis se viese mejor los resultados.

Destacar que existe una relacion entre categoria-precio y entre categoria-duracion.
```
def generar_entry_tatuaje():
    fake = Faker()
    
    # Definir probabilidades más realistas para las categorías
    categoria = random.choices(
        ["pequeño", "mediano", "grande"],
        weights=[0.5, 0.35, 0.15],  # Mayor probabilidad de tatuajes pequeños
        k=1
    )[0]
    
    if categoria == "pequeño":
        precio = random.randint(50, 200)
        duracion = random.randint(30, 90)  # En minutos
    elif categoria == "mediano":
        precio = random.randint(200, 500)
        duracion = random.randint(90, 180)
    else:  # grande
        precio = random.randint(500, 2000)
        duracion = random.randint(180, 480)
    
    
    # Probabilidades ajustadas para el método de pago
    metodo_pago = random.choices(["efectivo", "tarjeta"], weights=[0.4, 0.6], k=1)[0]
    
    # Probabilidades irregulares para edad y procedencia
    edad = random.choices(range(18, 61), weights=[5]*10 + [10]*10 + [15]*10 + [10]*10 + [5]*3, k=1)[0]
    procedencia = random.choices(
        [fake.city() for _ in range(10)],
        weights=[0.4, 0.15, 0.15, 0.1, 0.05, 0.05, 0.05, 0.025, 0.025, 0.025],
        k=1
    )[0]
    
    # Generar fechas con distribución por semanas (más actividad los fines de semana)
    fecha_hora = fake.date_time_between(start_date="-1y", end_date="now")
    while fecha_hora.weekday() in random.choices([0, 1, 2, 3, 4, 5, 6], weights=[5, 10, 15, 15, 20, 20, 15], k=1):
        fecha_hora = fake.date_time_between(start_date="-1y", end_date="now")
    fecha = fecha_hora.date().strftime("%Y-%m-%d")
    
    # Generar horas con pesos (más actividad en ciertas horas del día)
    horas_distribuidas = random.choices(
        range(9, 22), 
        weights=[5, 5, 10, 15, 20, 20, 15, 10, 5, 5, 5, 5, 5],
        k=1
    )[0]
    minutos = random.randint(0, 59)
    hora = (datetime.min + timedelta(hours=horas_distribuidas, minutes=minutos)).time().strftime("%H:%M")

    # Generar un id cliente con posibilidad de repetirse para simular distintas visitas de un cliente
    id_cliente = random.randint(0,50000)
    
    entry = {
        "id_venta": fake.uuid4(),
        "id_cliente": id_cliente,
        "nombre": fake.name(),
        "edad": edad,
        "procedencia": procedencia,
        "fecha": fecha,
        "hora": hora,
        "categoria": categoria,
        "duracion_min": duracion,
        "precio": precio,
        "metodo_pago": metodo_pago
    }
    
    return entry

```



Gracias a la libreria pandas, matplotlib y seaborn podemos ver los datos con gráficas con un par de lineas de código.


## Gráficas según sobre un eje
![image](https://github.com/user-attachments/assets/9288439b-28e6-4b1a-8dac-2e46ed88bc82)

En esta imagen vemos cuantos tipos de de tatuajes hay según categorías.
Nótese los pesos de la función que los genera, que son 50%, 35% y 15%


![image](https://github.com/user-attachments/assets/55e3e456-d426-45e7-b613-3eb610e24150)

En esta imagen vemos la cantidad de ventas en cada día de la semana.


![image](https://github.com/user-attachments/assets/e070ba98-a6b2-4f2a-875f-b968f82ee414)

Aquí, las ventas por hora, destacando las horas con más ventas según la media.


## Gráficas sobre dos ejes

![image](https://github.com/user-attachments/assets/d43dab79-7be9-4ef0-9551-2e94d143e9fc)

En esta imagen podemos ver el precio de los tatuajes según por categoría.


![image](https://github.com/user-attachments/assets/e8976437-50d5-4267-b2f3-84a2a6381836)

Por último, en esta imagen se puede ver claramente la artificialidad de los datos donde se puede apreciar los rangos generados de duracion en minutos y el precio

