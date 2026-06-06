# Síntesis ejecutiva de interpretación SHAP — Iteración 9

- **classification | EXP | failure_bin**: La probabilidad de falla de EXP está dominada por square (61.5%), lo que sugiere control por escala transversal y distorsión geométrica.
- **classification | KELVIN | failure_bin**: La falla en Kelvin no depende de una sola variable; cell_size (30.6%) lidera, pero los ángulos y el diámetro del alambre también aportan de forma relevante.
- **classification | SCHWARZ | failure_bin**: La falla de Schwarz está fuertemente gobernada por node_diameter_to_cell_size (73.3%), validando la importancia de ratios adimensionales para capturar restricciones relativas CAD.
- **regression | BCC | surface_area_m2**: El área superficial de BCC está liderada por D_toro (41.9%), junto con el diámetro del alambre; domina la escala global de la geometría.
- **regression | EXP | surface_area_m2**: El área superficial de EXP está liderada por height (47.7%), seguida por la dimensión transversal; domina el tamaño global de la celda.
- **regression | KELVIN | surface_area_m2**: El área superficial de Kelvin está liderada por cell_size (44.0%), con aporte relevante del diámetro del alambre y la discretización circular.
- **regression | SCHWARZ | surface_area_m2**: El área superficial de Schwarz está dominada por cell_size (78.0%), por lo que el área se comporta principalmente como función de escala global.
