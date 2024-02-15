# Medidas de inteligencia de tiempo
Las medidas de inteligencia de tiempos es uno de los aspectos mas importantes en el momento de generar reportes en Power BI, por lo anterior dejo esta reseta DAX con el objetivo ser usanda en los reportes en cuyos valores quieren comparar tiempos. 
### funcion ´DATEADD´
La funcion DATEADD es la funcion que permite a travez de la tabla calendario, tomar periodos anteriores a fin de generar la comparacion entre periodos, la sintaxis es  ```DATEADD(<dates>,<number_of_intervals>,<interval>)``` 
 - Aspectos importantes: Para dar poder llevar a cabe este proceso recomiento tener una metidda base ``` Matriculas = SUM(Historico_Runt_Autocom[Cantidad]) ```, esto se tomara como base para modificar el contexto de filtro a traves de la funcion calculate. 

```sql
VAR fecha = CALCULATE(MAX(Historico_Runt_Autocom[FECHA_MATRICULA]),REMOVEFILTERS(Calendario))
VAR anio_vegente = YEAR(fecha) 
VAR mes_vigente = MONTH(fecha)
VAR dia_vigente = DAY(fecha)
VAR fecha_periodos_anteriores = MAX(Historico_Runt_Autocom[FECHA_MATRICULA])

VAR fecha_vigente = 
-- fecha_vigente retorna la fecha hasta el dia actual de los meses anteriores
    IF ( 
        CALCULATE(
            MAX(Historico_Runt_Autocom[FECHA_MATRICULA]),
            REMOVEFILTERS(Calendario)) = MAX(Historico_Runt_Autocom[FECHA_MATRICULA]) ,fecha ,fecha_periodos_anteriores)
-- 
VAR calculo_periodos =
IF ( fecha_vigente = fecha,
/* calculo_periodos: Evaluar la fecha en el contexto antual, si esta el mes actual retorna el acumulado de los meses anteriores hasta el dia en curso, si es un mes distinto toma el acumulados del mes completo. esto con el objeto de pasar un KPI y comparar la medida hasta el dia actual de los mese anteriores
*/
    CALCULATE ( 
        [Matriculas] ,
        DATEADD ( 
             FILTER ( DATESMTD ( Calendario[Date] ) , Calendario[Date] < fecha_vigente ) , -1 , MONTH ) ),
                 CALCULATE ( 
        [Matriculas] , 
        DATEADD ( 
             FILTER ( DATESMTD ( Calendario[Date] ) , Calendario[Date] <= fecha_vigente ) , -1 , MONTH )
    )
    )
RETURN 
    IF ( ISBLANK( calculo_periodos ) , BLANK() , calculo_periodos )
```
