# Medidas Cumulativa
Las medidas cumulativas sn importantes dado que nos muestran los total a un corte determinado y desde alli podemos trabajar KPI de participacion
```sql
CALCULATE (
  [Ventas] ,
    FILTER ( CALENDARIO ,  Calendario[date] >= MAX(Calendario[date])
```
