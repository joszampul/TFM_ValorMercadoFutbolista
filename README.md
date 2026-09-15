# Modelización predictiva del valor de mercado de un futbolista

Trabajo Fin de Máster · Máster en Big Data, Data Science e Inteligencia Artificial
Universidad Complutense de Madrid · Curso 2025/2026

**Jose Manuel Zamora Pulido**

---

## Qué hace

Estima el valor de mercado de un futbolista profesional a partir de su rendimiento en
competición, su perfil y el contexto de su club, y cuantifica mediante valores SHAP qué
factores determinan esa valoración.

La unidad de análisis es el binomio **jugador-temporada**. El modelo predice la desviación
logarítmica respecto al nivel general del mercado, no una cantidad absoluta en euros.

**Alcance:** cinco grandes ligas europeas, temporadas 2016/17 a 2023/24 (excluida la
2019/20), jugadores de campo con al menos 450 minutos disputados. 12.861 observaciones.

**Resultado:** R² de 0,837 y MAE de 4,37 M€ sobre la temporada 2023/24, no observada
durante el entrenamiento y sin emplear la valoración previa del jugador.

---

## Reproducción

```bash
python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter lab
```

Ejecutar los notebooks **en orden**: `01` construye el conjunto analítico que consume `02`,
que a su vez serializa el modelo que utiliza `03`.

### Datos

El conjunto procede de [Football Data from Transfermarkt](https://www.kaggle.com/datasets/davidcariboo/player-scores)
(licencia CC0) y se descarga automáticamente mediante `kagglehub`, por lo que se requieren
credenciales de Kaggle en `~/.kaggle/kaggle.json`.

> **Nota sobre reproducibilidad.** El conjunto se actualiza semanalmente. Los resultados de
> la memoria corresponden a la **versión 677**. Una ejecución posterior descargará la versión
> vigente y producirá cifras ligeramente distintas.

---

## Notas metodológicas

- **Diseño temporal.** Ningún predictor emplea información posterior al 1 de mayo. La
  variable objetivo se toma en la ventana del 1 de mayo al 15 de julio, amplia porque las
  ligas no revisan sus valoraciones simultáneamente.
- **Validación out-of-time.** Partición por temporadas completas (entrenamiento hasta
  2021/22, validación 2022/23, prueba 2023/24) y `GroupKFold` por jugador. Una partición
  aleatoria infla el R² de 0,818 a 0,859.
- **Deflactación.** `y = ln(V) − ln(I)`, donde `I` es la mediana de las tasaciones del verano
  anterior en las cinco grandes ligas.
- **Retrotransformación.** Se aplica la corrección de Duan (1983) al deshacer el logaritmo.

---

## Limitaciones conocidas

- No se dispone de variables contractuales ni de historial de lesiones, que explican buena
  parte de los residuos extremos.
- El intervalo de predicción al 80 % alcanza una cobertura empírica del 73,5 %.
- La variable objetivo es una tasación estimada, no un precio de traspaso realmente pagado.

---

## Licencia

Código bajo licencia MIT. El conjunto de datos original se distribuye bajo CC0.
