# Medidas diagnostico

Brecha_Hospedados_TurismoInterno = 
[Recup. Viaj. Hospedados %] - [Recuperacion Turismo Interno %]

Indicador seleccionado = 
SWITCH(
    SELECTEDVALUE(Indicador_Mapa[Indicador], "Turistas internos 2023"),
    "Viajeros hospedados 2023", [Viajeros Hospedados 2023],
    "Turistas internos 2023", [Turistas Internos 2023],
    [Turistas Internos 2023]
)


# Medidas Hoteleria

COVID = 
DIVIDE (
    [Viajeros Hospedados COVID],
    [Promedio Viajeros Hospedados Pre-COVID]
)

Estadía Media = 
DIVIDE(
    [Total Pernoctes],
    [Total Viajeros Hospedados]
)

Ocupación Habitaciones = 
AVERAGE(FACT_Hoteleria_Ocupacion[Ocupacion_Habitaciones])


Ocupación Habitaciones Mostrada = 
IF(
    ISFILTERED(DIM_Categoria_Hotelera[Categoria_Hotelera]),
    [Ocupación Habitaciones],
    CALCULATE(
        [Ocupación Habitaciones],
        DIM_Categoria_Hotelera[Categoria_Hotelera] = "Total"
    )
)


Ocupación Plazas = 
AVERAGE(FACT_Hoteleria_Ocupacion[Ocupacion_Plazas])


Ocupación Plazas Mostrada = 
IF(
    ISFILTERED(DIM_Categoria_Hotelera[Categoria_Hotelera]),
    [Ocupación Plazas],
    CALCULATE(
        [Ocupación Plazas],
        DIM_Categoria_Hotelera[Categoria_Hotelera] = "Total"
    )
)



# Medidas Viajeros 

Promedio Viajeros Hospedados Pre-COVID = 
DIVIDE (
    CALCULATE (
        [Total Viajeros Hospedados],
        REMOVEFILTERS ( DIM_Fecha[Anio] ),
        DIM_Fecha[Anio] = 2017
    )
    +
    CALCULATE (
        [Total Viajeros Hospedados],
        REMOVEFILTERS ( DIM_Fecha[Anio] ),
        DIM_Fecha[Anio] = 2018
    )
    +
    CALCULATE (
        [Total Viajeros Hospedados],
        REMOVEFILTERS ( DIM_Fecha[Anio] ),
        DIM_Fecha[Anio] = 2019
    ),
    3
)



Promedio Viajeros Hospedados Recuperación = 
    CALCULATE (
        [Total Viajeros Hospedados],
        REMOVEFILTERS ( DIM_Fecha[Anio] ),
        DIM_Fecha[Anio] = 2023
    )



Recup. Viaj. Hospedados % = 
DIVIDE(
    [Viajeros Hospedados 2023],
    [Viajeros Promedio Pre-COVID]
)



Total Pernoctes = 
SUM(FACT_Hoteleria_Demanda[Pernoctes])



Total Viajeros Hospedados = 
SUM(FACT_Hoteleria_Demanda[Viajeros])


Viajeros Hospedados 2023 = 
CALCULATE(
    [Total Viajeros Hospedados],
    REMOVEFILTERS(DIM_Fecha),
    DIM_Fecha[Anio] = 2023
)



Viajeros Hospedados COVID = 
CALCULATE (
    [Total Viajeros Hospedados],
    REMOVEFILTERS ( DIM_Fecha[Anio] ),
    DIM_Fecha[Anio] = 2020
)


Viajeros Promedio Pre-COVID = 
AVERAGEX(
    {2017, 2018, 2019},
    VAR AnioPreCovid = [Value]
    RETURN
        CALCULATE(
            [Total Viajeros Hospedados],
            REMOVEFILTERS(DIM_Fecha),
            DIM_Fecha[Anio] = AnioPreCovid
        )
)




# Recuperación

Recuperación 2023 = 
DIVIDE (
    [Promedio Viajeros Hospedados Recuperación],
    [Promedio Viajeros Hospedados Pre-COVID]
)


Recuperación Viajeros % = 
DIVIDE(
    [Viajeros Hospedados 2023],
    [Promedio Viajeros Hospedados Pre-COVID]
)


# Medidas Turismo Interno

Estadía Media Turismo Interno = 
DIVIDE (
    [Total Pernoctaciones Internas],
    [Total Turistas Internos]
)

Gasto Promedio por Turista = 
DIVIDE (
    [Total Gasto Turístico],
    [Total Turistas Internos]
)


Recuperacion Turismo Interno % = 
// Recuperación del turismo interno: 2023 vs promedio 2017-2019.
DIVIDE(
    [Turistas Internos 2023],
    [Turistas Internos Promedio Pre-COVID]
)


Total Gasto Turístico = SUM(FACT_TurismoInterno_Regional[Gasto_Total_Pesos_Corrientes])


Total Pernoctaciones Internas = 
SUM ( FACT_TurismoInterno_Regional[Pernoctaciones_Turismo_Interno] )


Total Turistas Internos = 
// Movimiento de turismo interno por región.
SUM(FACT_TurismoInterno_Regional[Turistas_Internos])


Turistas Internos 2023 = 
// Turismo interno en 2023 para comparar contra el período pre-COVID.
CALCULATE(
    [Total Turistas Internos],
    REMOVEFILTERS(DIM_Fecha),
    DIM_Fecha[Anio] = 2023
)



Turistas Internos Promedio Pre-COVID = 
// Promedio anual de turismo interno entre 2017 y 2019.
AVERAGEX(
    {2017, 2018, 2019},
    VAR AnioPreCovid = [Value]
    RETURN
        CALCULATE(
            [Total Turistas Internos],
            REMOVEFILTERS(DIM_Fecha),
            DIM_Fecha[Anio] = AnioPreCovid
        )
)


# Medidas Color

Color_Gasto_Turistico_Consolidado = 
VAR _Actual = [Total Gasto Turístico]
VAR _AnioSeleccionado = SELECTEDVALUE(DIM_Fecha[Anio])
VAR _AnioFiltrado = ISFILTERED(DIM_Fecha[Anio])

VAR _MesesSeleccionados = CALCULATETABLE(VALUES(DIM_Fecha[Mes_Nombre]), ALLSELECTED(DIM_Fecha))

VAR _Anterior = 
    IF(
        NOT _AnioFiltrado,
        BLANK(),
        CALCULATE(
            [Total Gasto Turístico],
            FILTER(ALL(DIM_Fecha[Anio]), DIM_Fecha[Anio] = _AnioSeleccionado - 1),
            TREATAS(_MesesSeleccionados, DIM_Fecha[Mes_Nombre])
        )
    )

VAR _Var = DIVIDE(_Actual - _Anterior, _Anterior)

RETURN
    SWITCH(
        TRUE(),
        // PRIORIDAD 1: Si se borraron filtros, pinta el número con color neutro (Gris oscuro/Negro)
        NOT(_AnioFiltrado), "#2D3748", 
        
        // PRIORIDAD 2: Si está filtrado pero los datos están vacíos
        ISBLANK(_Actual), "#2D3748",
        
        // PRIORIDAD 3: Si está filtrado pero no hay histórico
        ISBLANK(_Anterior), "#2D3748",
        
        // Variaciones normales de color
        _Var > 0, "#00897B", // Verde
        _Var < 0, "#E53935", // Rojo
        "#2D3748"
    )


Color_Ocup_Habitacional_Consolidado = 
VAR _AniosEnContexto = COUNTROWS(VALUES(DIM_Fecha[Anio]))
VAR _HayUnSoloAnio = _AniosEnContexto = 1
VAR _AnioActual = MAX(DIM_Fecha[Anio])
VAR _Actual = [Ocupación Habitaciones]
VAR _Anterior = 
    CALCULATE(
        [Ocupación Habitaciones],
        ALLEXCEPT(DIM_Fecha, DIM_Fecha[Anio]),
        DIM_Fecha[Anio] = _AnioActual - 1
    )
VAR _Var = DIVIDE(_Actual - _Anterior, _Anterior, BLANK())
RETURN
    SWITCH(TRUE(),
        ISBLANK(_Actual), "#2D3748",
        -- Sin año seleccionado: color por umbral fijo
        NOT _HayUnSoloAnio, 
            SWITCH(TRUE(),
                _Actual >= 0.41, "#00897B",
                _Actual >= 0.3, "#2D3748",
                "#E53935"
            ),
        -- Con año seleccionado: color por variación
        ISBLANK(_Anterior), "#2D3748",
        _Var > 0, "#00897B",
        _Var < 0, "#E53935",
        "#2D3748"
    )


	Color_Pernoctes_Consolidado = 
VAR _Actual = [Total Pernoctes]
VAR _AnioSeleccionado = SELECTEDVALUE(DIM_Fecha[Anio])
VAR _AnioFiltrado = ISFILTERED(DIM_Fecha[Anio])

VAR _MesesSeleccionados = CALCULATETABLE(VALUES(DIM_Fecha[Mes_Nombre]), ALLSELECTED(DIM_Fecha))

VAR _Anterior = 
    IF(
        NOT _AnioFiltrado,
        BLANK(),
        CALCULATE(
            [Total Pernoctes],
            FILTER(ALL(DIM_Fecha[Anio]), DIM_Fecha[Anio] = _AnioSeleccionado - 1),
            TREATAS(_MesesSeleccionados, DIM_Fecha[Mes_Nombre])
        )
    )

VAR _Var = DIVIDE(_Actual - _Anterior, _Anterior)

RETURN
    SWITCH(
        TRUE(),
        // PRIORIDAD 1: Si se borraron filtros, pinta el número con color neutro (Gris oscuro/Negro)
        NOT(_AnioFiltrado), "#2D3748", 
        
        // PRIORIDAD 2: Si está filtrado pero los datos están vacíos
        ISBLANK(_Actual), "#2D3748",
        
        // PRIORIDAD 3: Si está filtrado pero no hay histórico
        ISBLANK(_Anterior), "#2D3748",
        
        // Variaciones normales de color
        _Var > 0, "#00897B", // Verde
        _Var < 0, "#E53935", // Rojo
        "#2D3748"
    )



Color_Turistas_Consolidado = 
VAR _Actual = [Total Turistas Internos]
VAR _AnioSeleccionado = SELECTEDVALUE(DIM_Fecha[Anio])
VAR _AnioFiltrado = ISFILTERED(DIM_Fecha[Anio])

VAR _MesesSeleccionados = CALCULATETABLE(VALUES(DIM_Fecha[Mes_Nombre]), ALLSELECTED(DIM_Fecha))

VAR _Anterior = 
    IF(
        NOT _AnioFiltrado,
        BLANK(),
        CALCULATE(
            [Total Turistas Internos],
            FILTER(ALL(DIM_Fecha[Anio]), DIM_Fecha[Anio] = _AnioSeleccionado - 1),
            TREATAS(_MesesSeleccionados, DIM_Fecha[Mes_Nombre])
        )
    )

VAR _Var = DIVIDE(_Actual - _Anterior, _Anterior)

RETURN
    SWITCH(
        TRUE(),
        // PRIORIDAD 1: Si se borraron filtros, pinta el número con color neutro (Gris oscuro/Negro)
        NOT(_AnioFiltrado), "#2D3748", 
        
        // PRIORIDAD 2: Si está filtrado pero los datos están vacíos
        ISBLANK(_Actual), "#2D3748",
        
        // PRIORIDAD 3: Si está filtrado pero no hay histórico
        ISBLANK(_Anterior), "#2D3748",
        
        // Variaciones normales de color
        _Var > 0, "#00897B", // Verde
        _Var < 0, "#E53935", // Rojo
        "#2D3748"
    )


	Periodo_Corto = 
SWITCH(
    SELECTEDVALUE('DIM_Fecha'[Periodo_Covid]),
    "Pre-COVID", "Antes",
    "COVID", "COVID",
    "Recuperacion", "Después"
)




# Medidas Títulos

Titulo_Gasto_Turistico = 
// 1. Detección de filtros activos (el usuario interactuó con el segmentador)
VAR _AnioFiltrado = ISFILTERED(DIM_Fecha[Anio])
VAR _RegionFiltrado = ISFILTERED(DIM_Region[Region])
VAR _MesFiltrado = ISFILTERED(DIM_Fecha[Mes_Nombre])
VAR _PeriodoFiltrado = ISFILTERED(DIM_Fecha[Periodo_Covid])

// Conteo de filtros activos para la lógica de compactación
VAR _CantFiltros = INT(_AnioFiltrado) + INT(_RegionFiltrado) + INT(_MesFiltrado) + INT(_PeriodoFiltrado)

// 2. Lógica para REGIÓN (Solo muestra si es selección ÚNICA)
VAR _ParteRegion = 
    IF(_RegionFiltrado && HASONEVALUE(DIM_Region[Region]), 
        " · " & VALUES(DIM_Region[Region]), 
        "" // Si es múltiple o no está filtrado, no muestra nada
    )

// 3. Lógica para FECHAS (Solo muestra si son selecciones ÚNICAS)
VAR _TieneUnAnio = HASONEVALUE(DIM_Fecha[Anio])
VAR _TieneUnMes = HASONEVALUE(DIM_Fecha[Mes_Nombre])

VAR _ParteFecha = 
    SWITCH(
        TRUE(),
        // Caso A: Año y Mes tienen selección única -> Formato MM/YYYY
        _AnioFiltrado && _MesFiltrado && _TieneUnAnio && _TieneUnMes,
            " · " & FORMAT(CALCULATE(MIN(DIM_Fecha[Mes_Numero])), "00") & "/" & VALUES(DIM_Fecha[Anio]),
        
        // Caso B: Solo el Año tiene selección única (Mes no está filtrado o es múltiple)
        _AnioFiltrado && _TieneUnAnio && (NOT _MesFiltrado || NOT _TieneUnMes),
            " · " & VALUES(DIM_Fecha[Anio]),
        
        // Caso C: Solo el Mes tiene selección única (Año no está filtrado o es múltiple)
        _MesFiltrado && _TieneUnMes && (NOT _AnioFiltrado || NOT _TieneUnAnio),
            IF(_CantFiltros >= 2, 
                " · " & FORMAT(CALCULATE(MIN(DIM_Fecha[Mes_Numero])), "00"), 
                " · " & VALUES(DIM_Fecha[Mes_Nombre])
            ),
            
        // Caso D: Cualquier otra combinación con selección múltiple -> No muestra nada
        ""
    )

// 4. Lógica para PERIODO COVID (Solo muestra si es selección ÚNICA)
VAR _PartePeriodo = 
    IF(_PeriodoFiltrado && HASONEVALUE(DIM_Fecha[Periodo_Covid]),
        VAR _PeriodoValor = VALUES(DIM_Fecha[Periodo_Covid])
        VAR _PeriodoCorto = 
            SWITCH(
                _PeriodoValor,
                "Pre-COVID", IF(_CantFiltros >= 2, "Pre", "Antes"),
                "COVID", "COVID",
                "Recuperacion", IF(_CantFiltros >= 2, "Post", "Después"),
                ""
            )
        RETURN " · " & _PeriodoCorto,
        "" // Si es múltiple o no está filtrado, no muestra nada
    )

RETURN
    "Total Gasto Turístico" & _ParteRegion & _ParteFecha & _PartePeriodo



	Titulo_GrafLinea_Gasto_Turistico = 
// 1. CAPTURA DE FILTROS: Forzamos la lectura de los filtros externos ignorando el contexto del gráfico
VAR _Anio = CALCULATE(SELECTEDVALUE(DIM_Fecha[Anio]), ALLSELECTED(DIM_Fecha[Anio]))
VAR _Region = CALCULATE(SELECTEDVALUE(DIM_Region[Region]), ALLSELECTED(DIM_Region[Region]))
VAR _MesNombre = CALCULATE(SELECTEDVALUE(DIM_Fecha[Mes_Nombre]), ALLSELECTED(DIM_Fecha[Mes_Nombre]))
VAR _MesNumero = INT(CALCULATE(SELECTEDVALUE(DIM_Fecha[Mes_Numero]), ALLSELECTED(DIM_Fecha[Mes_Numero])))
VAR _Periodo = CALCULATE(SELECTEDVALUE(DIM_Fecha[Periodo_Covid]), ALLSELECTED(DIM_Fecha[Periodo_Covid]))

// 2. VALIDACIÓN DE FILTROS ACTIVOS (Detecta si el usuario hizo clic en los segmentadores de la página)
VAR _AnioFiltrado = ISFILTERED(DIM_Fecha[Anio])
VAR _RegionFiltrado = ISFILTERED(DIM_Region[Region])
VAR _MesFiltrado = ISFILTERED(DIM_Fecha[Mes_Nombre])
VAR _PeriodoFiltrado = ISFILTERED(DIM_Fecha[Periodo_Covid])

// Conteo de filtros activos para la lógica de visualización compacta
VAR _CantFiltros = INT(_AnioFiltrado) + INT(_RegionFiltrado) + INT(_MesFiltrado) + INT(_PeriodoFiltrado)

// 3. CONSTRUCCIÓN DE FRAGMENTOS DEL TEXTO
VAR _ParteRegion = IF(NOT _RegionFiltrado, "", " · " & _Region)

// Formato de Fecha (Aplica regla MM/YYYY si se eligió Mes y Año a la vez)
VAR _ParteFecha = 
    IF(_AnioFiltrado && _MesFiltrado,
        " · " & FORMAT(_MesNumero, "00") & "/" & _Anio,
        IF(NOT _AnioFiltrado, "", " · " & _Anio) & 
        IF(NOT _MesFiltrado, "",
            IF(_CantFiltros >= 2, " · " & FORMAT(_MesNumero, "00"), " · " & _MesNombre)
        )
    )

// Formato de Periodo COVID
VAR _PeriodoCorto = 
    IF(NOT _PeriodoFiltrado, "",
        SWITCH(
            _Periodo,
            "Pre-COVID", IF(_CantFiltros >= 2, "Pre", "Antes"),
            "COVID", "COVID",
            "Recuperacion", IF(_CantFiltros >= 2, "Post", "Después"),
            ""
        )
    )

VAR _PartePeriodo = IF(_PeriodoCorto = "", "", " · " & _PeriodoCorto)

RETURN
    "Evolución Gasto Turístico" & _ParteRegion & _ParteFecha & _PartePeriodo



Titulo_Ocup_Habitacional = 
// 1. Detección de filtros activos (el usuario interactuó con el segmentador)
VAR _AnioFiltrado = ISFILTERED(DIM_Fecha[Anio])
VAR _RegionFiltrado = ISFILTERED(DIM_Region[Region])
VAR _MesFiltrado = ISFILTERED(DIM_Fecha[Mes_Nombre])
VAR _PeriodoFiltrado = ISFILTERED(DIM_Fecha[Periodo_Covid])

// Conteo de filtros activos para la lógica de compactación
VAR _CantFiltros = INT(_AnioFiltrado) + INT(_RegionFiltrado) + INT(_MesFiltrado) + INT(_PeriodoFiltrado)

// 2. Lógica para REGIÓN (Solo muestra si es selección ÚNICA)
VAR _ParteRegion = 
    IF(_RegionFiltrado && HASONEVALUE(DIM_Region[Region]), 
        " · " & VALUES(DIM_Region[Region]), 
        "" // Si es múltiple o no está filtrado, no muestra nada
    )

// 3. Lógica para FECHAS (Solo muestra si son selecciones ÚNICAS)
VAR _TieneUnAnio = HASONEVALUE(DIM_Fecha[Anio])
VAR _TieneUnMes = HASONEVALUE(DIM_Fecha[Mes_Nombre])

VAR _ParteFecha = 
    SWITCH(
        TRUE(),
        // Caso A: Año y Mes tienen selección única -> Formato MM/YYYY
        _AnioFiltrado && _MesFiltrado && _TieneUnAnio && _TieneUnMes,
            " · " & FORMAT(CALCULATE(MIN(DIM_Fecha[Mes_Numero])), "00") & "/" & VALUES(DIM_Fecha[Anio]),
        
        // Caso B: Solo el Año tiene selección única (Mes no está filtrado o es múltiple)
        _AnioFiltrado && _TieneUnAnio && (NOT _MesFiltrado || NOT _TieneUnMes),
            " · " & VALUES(DIM_Fecha[Anio]),
        
        // Caso C: Solo el Mes tiene selección única (Año no está filtrado o es múltiple)
        _MesFiltrado && _TieneUnMes && (NOT _AnioFiltrado || NOT _TieneUnAnio),
            IF(_CantFiltros >= 2, 
                " · " & FORMAT(CALCULATE(MIN(DIM_Fecha[Mes_Numero])), "00"), 
                " · " & VALUES(DIM_Fecha[Mes_Nombre])
            ),
            
        // Caso D: Cualquier otra combinación con selección múltiple -> No muestra nada
        ""
    )

// 4. Lógica para PERIODO COVID (Solo muestra si es selección ÚNICA)
VAR _PartePeriodo = 
    IF(_PeriodoFiltrado && HASONEVALUE(DIM_Fecha[Periodo_Covid]),
        VAR _PeriodoValor = VALUES(DIM_Fecha[Periodo_Covid])
        VAR _PeriodoCorto = 
            SWITCH(
                _PeriodoValor,
                "Pre-COVID", IF(_CantFiltros >= 2, "Pre", "Antes"),
                "COVID", "COVID",
                "Recuperacion", IF(_CantFiltros >= 2, "Post", "Después"),
                ""
            )
        RETURN " · " & _PeriodoCorto,
        "" // Si es múltiple o no está filtrado, no muestra nada
    )



Titulo_Turistas = 
// 1. Detección de filtros activos (el usuario interactuó con el segmentador)
VAR _AnioFiltrado = ISFILTERED(DIM_Fecha[Anio])
VAR _RegionFiltrado = ISFILTERED(DIM_Region[Region])
VAR _MesFiltrado = ISFILTERED(DIM_Fecha[Mes_Nombre])
VAR _PeriodoFiltrado = ISFILTERED(DIM_Fecha[Periodo_Covid])

// Conteo de filtros activos para la lógica de compactación
VAR _CantFiltros = INT(_AnioFiltrado) + INT(_RegionFiltrado) + INT(_MesFiltrado) + INT(_PeriodoFiltrado)

// 2. Lógica para REGIÓN (Solo muestra si es selección ÚNICA)
VAR _ParteRegion = 
    IF(_RegionFiltrado && HASONEVALUE(DIM_Region[Region]), 
        " · " & VALUES(DIM_Region[Region]), 
        "" // Si es múltiple o no está filtrado, no muestra nada
    )

// 3. Lógica para FECHAS (Solo muestra si son selecciones ÚNICAS)
VAR _TieneUnAnio = HASONEVALUE(DIM_Fecha[Anio])
VAR _TieneUnMes = HASONEVALUE(DIM_Fecha[Mes_Nombre])

VAR _ParteFecha = 
    SWITCH(
        TRUE(),
        // Caso A: Año y Mes tienen selección única -> Formato MM/YYYY
        _AnioFiltrado && _MesFiltrado && _TieneUnAnio && _TieneUnMes,
            " · " & FORMAT(CALCULATE(MIN(DIM_Fecha[Mes_Numero])), "00") & "/" & VALUES(DIM_Fecha[Anio]),
        
        // Caso B: Solo el Año tiene selección única (Mes no está filtrado o es múltiple)
        _AnioFiltrado && _TieneUnAnio && (NOT _MesFiltrado || NOT _TieneUnMes),
            " · " & VALUES(DIM_Fecha[Anio]),
        
        // Caso C: Solo el Mes tiene selección única (Año no está filtrado o es múltiple)
        _MesFiltrado && _TieneUnMes && (NOT _AnioFiltrado || NOT _TieneUnAnio),
            IF(_CantFiltros >= 2, 
                " · " & FORMAT(CALCULATE(MIN(DIM_Fecha[Mes_Numero])), "00"), 
                " · " & VALUES(DIM_Fecha[Mes_Nombre])
            ),
            
        // Caso D: Cualquier otra combinación con selección múltiple -> No muestra nada
        ""
    )

// 4. Lógica para PERIODO COVID (Solo muestra si es selección ÚNICA)
VAR _PartePeriodo = 
    IF(_PeriodoFiltrado && HASONEVALUE(DIM_Fecha[Periodo_Covid]),
        VAR _PeriodoValor = VALUES(DIM_Fecha[Periodo_Covid])
        VAR _PeriodoCorto = 
            SWITCH(
                _PeriodoValor,
                "Pre-COVID", IF(_CantFiltros >= 2, "Pre", "Antes"),
                "COVID", "COVID",
                "Recuperacion", IF(_CantFiltros >= 2, "Post", "Después"),
                ""
            )
        RETURN " · " & _PeriodoCorto,
        "" // Si es múltiple o no está filtrado, no muestra nada
    )

RETURN
    "Total Turistas" & _ParteRegion & _ParteFecha & _PartePeriodo





Titulo_Viajeros = 
// 1. Detección de filtros activos (el usuario interactuó con el segmentador)
VAR _AnioFiltrado = ISFILTERED(DIM_Fecha[Anio])
VAR _RegionFiltrado = ISFILTERED(DIM_Region[Region])
VAR _MesFiltrado = ISFILTERED(DIM_Fecha[Mes_Nombre])
VAR _PeriodoFiltrado = ISFILTERED(DIM_Fecha[Periodo_Covid])

// Conteo de filtros activos para la lógica de compactación
VAR _CantFiltros = INT(_AnioFiltrado) + INT(_RegionFiltrado) + INT(_MesFiltrado) + INT(_PeriodoFiltrado)

// 2. Lógica para REGIÓN (Solo muestra si es selección ÚNICA)
VAR _ParteRegion = 
    IF(_RegionFiltrado && HASONEVALUE(DIM_Region[Region]), 
        " · " & VALUES(DIM_Region[Region]), 
        "" // Si es múltiple o no está filtrado, no muestra nada
    )

// 3. Lógica para FECHAS (Solo muestra si son selecciones ÚNICAS)
VAR _TieneUnAnio = HASONEVALUE(DIM_Fecha[Anio])
VAR _TieneUnMes = HASONEVALUE(DIM_Fecha[Mes_Nombre])

VAR _ParteFecha = 
    SWITCH(
        TRUE(),
        // Caso A: Año y Mes tienen selección única -> Formato MM/YYYY
        _AnioFiltrado && _MesFiltrado && _TieneUnAnio && _TieneUnMes,
            " · " & FORMAT(CALCULATE(MIN(DIM_Fecha[Mes_Numero])), "00") & "/" & VALUES(DIM_Fecha[Anio]),
        
        // Caso B: Solo el Año tiene selección única (Mes no está filtrado o es múltiple)
        _AnioFiltrado && _TieneUnAnio && (NOT _MesFiltrado || NOT _TieneUnMes),
            " · " & VALUES(DIM_Fecha[Anio]),
        
        // Caso C: Solo el Mes tiene selección única (Año no está filtrado o es múltiple)
        _MesFiltrado && _TieneUnMes && (NOT _AnioFiltrado || NOT _TieneUnAnio),
            IF(_CantFiltros >= 2, 
                " · " & FORMAT(CALCULATE(MIN(DIM_Fecha[Mes_Numero])), "00"), 
                " · " & VALUES(DIM_Fecha[Mes_Nombre])
            ),
            
        // Caso D: Cualquier otra combinación con selección múltiple -> No muestra nada
        ""
    )

// 4. Lógica para PERIODO COVID (Solo muestra si es selección ÚNICA)
VAR _PartePeriodo = 
    IF(_PeriodoFiltrado && HASONEVALUE(DIM_Fecha[Periodo_Covid]),
        VAR _PeriodoValor = VALUES(DIM_Fecha[Periodo_Covid])
        VAR _PeriodoCorto = 
            SWITCH(
                _PeriodoValor,
                "Pre-COVID", IF(_CantFiltros >= 2, "Pre", "Antes"),
                "COVID", "COVID",
                "Recuperacion", IF(_CantFiltros >= 2, "Post", "Después"),
                ""
            )
        RETURN " · " & _PeriodoCorto,
        "" // Si es múltiple o no está filtrado, no muestra nada
    )

RETURN
    "Total Viajeros" & _ParteRegion & _ParteFecha & _PartePeriodo


# KPIs


KPI_Gasto_Turistico_Visual = CALCULATE([Total Gasto Turístico], 'Grupo de cálculo'[Color KPI] = "Variación Anual")


KPI_Ocup_Habitacional_Visual = 
VAR _TodosLosAnios = COUNTROWS(ALL(DIM_Fecha[Anio]))
VAR _AniosEnContexto = COUNTROWS(VALUES(DIM_Fecha[Anio]))
VAR _HayUnSoloAnio = _AniosEnContexto = 1
VAR _AnioActual = MAX(DIM_Fecha[Anio])
VAR _Actual = [Ocupación Habitaciones]
VAR _Anterior = 
    CALCULATE(
        [Ocupación Habitaciones],
        ALLEXCEPT(DIM_Fecha, DIM_Fecha[Anio]),
        DIM_Fecha[Anio] = _AnioActual - 1
    )
VAR _Var = DIVIDE(_Actual - _Anterior, _Anterior, BLANK())
RETURN
    SWITCH(TRUE(),
        NOT _HayUnSoloAnio, FORMAT(_Actual, "0.0%"),
        ISBLANK(_Anterior), FORMAT(_Actual, "0.0%"),
        _Var > 0, "▲ " & FORMAT(_Var, "0.0%"),
        _Var < 0, "▼ " & FORMAT(ABS(_Var), "0.0%"),
        FORMAT(_Actual, "0.0%")
    )


KPI_Pernoctes_Visual = CALCULATE([Total Viajeros Hospedados], 'Grupo de cálculo'[Color KPI] = "Variación Anual")


KPI_Turistas_Visual = CALCULATE([Total Turistas Internos], 'Grupo de cálculo'[Color KPI] = "Variación Anual")


# Indicadores

Indicador Ranking Regional = {
    ("Total Viajeros Hospedados", NAMEOF('MEDIDAS_Hoteleria'[Total Viajeros Hospedados]), 0),
    ("Total Turistas Internos", NAMEOF('MEDIDAS_TurismoInterno'[Total Turistas Internos]), 2)
}


Indicador_Mapa = 
DATATABLE(
    "Indicador", STRING,
    "Orden", INTEGER,
    {
        {"Viajeros hospedados 2023", 1},
        {"Turistas internos 2023", 2},
        {"Pasajeros aéreos 2023", 3}
    }
)


