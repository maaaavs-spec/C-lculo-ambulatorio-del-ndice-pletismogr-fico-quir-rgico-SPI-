# Calculo ambulatorio del indice pletismografico quirurgico (SPI)
# **Integrantes**
>
* María Angélica Vargas Saldaña 5600820
* Samuel Esteban Fonseca Luna 5600808
* Laura Daniela Triana Molano 5600828

<h2 align="center">𝙞𝙣𝙩𝙧𝙤𝙙𝙪𝙘𝙘𝙞ó𝙣</h2>

La monitorización de la nocicepción durante las intervenciones quirúrgicas es muy importante para estimar la reacción fisiológica del paciente y propiciar un balance apropiado entre analgesia y nocicepción. El Índice Pletismográfico Quirúrgico (SPI) es uno de los métodos creados para este fin, al ser este método funciona empleado propiedades de la onda fotopletismografía (PPG) para calcular las variaciones relacionadas con la reacción nociceptiva. El SPI se expresa dentro de una escala que va del 0 al 100; en ella, los valores más altos indican una respuesta nociceptiva más amplia.

En esta práctica se desarrolló un sistema de adquisición y procesamiento de una señal PPG para obtener características de la onda de pulso y realizar el cálculo del SPI en condiciones ambulatorias. Para esto, se utilizó un sensor óptico MAX30102 conectado a un ESP32, lo que posibilitó la adquisición continua de la señal y su procesamiento a través de MATLAB. Se determinaron parámetros como los intervalos entre pulsos y la amplitud de la onda a partir de la señal adquirida, que fueron empleados posteriormente en el cálculo experimental del índice.

El desarrollo de esta práctica permite posible la conexión entre la adquisición de señales fisiológicas, su análisis digital y la interpretación de respuestas vinculadas con la nocicepción. Además, se pretende analizar las discrepancias entre la interpretación del SPI y la respuesta fisiológica observada. Esto es así porque el SPI es un índice que permite calcular la nocicepción, aunque no representa una medición directa de la percepción subjetiva del dolor. La práctica propone específicamente identificar estas características y examinar las restricciones del sistema desarrollado.

<img width="687" height="434" alt="image" src="https://github.com/user-attachments/assets/b99cf639-575e-4ece-b454-08b1229f1442" />

(MAX30102 - Sensor de Concentración de Oxígeno y Ritmo Cardíaco - Electronilab, 2026)



<h2 align="center">𝙤𝙗𝙟𝙚𝙩𝙞𝙫𝙤𝙨</h2>

𝙊𝙗𝙟𝙚𝙩𝙞𝙫𝙤 𝙜𝙚𝙣𝙚𝙧𝙖𝙡: 
Desarrollar un sistema de adquisición y procesamiento de una señal fotopletismográfica para obtener el Índice Pletismográfico Quirúrgico (SPI) durante una captura ambulatoria de 120 segundos.

𝙊𝙗𝙟𝙚𝙩𝙞𝙫𝙤𝙨 𝙀𝙨𝙥𝙚𝙘𝙞́𝙛𝙞𝙘𝙤𝙨:
+ Adquirir la señal PPG mediante el sensor MAX30102 y una ESP32.
+ Procesar la señal PPG para reducir componentes no deseadas.
+ Detectar los máximos y mínimos de la señal.
+ Calcular el intervalo entre pulsaciones (HBI).
+ Calcular el SPI para cada pulsación.
+ Analizar la evolución del SPI durante las tres etapas de la prueba.


𝙎𝙚𝙣𝙨𝙤𝙧 𝙚𝙨𝙘𝙤𝙜𝙞𝙙𝙤 
El MAX30102 es un sensor óptico de la compañía Maxim Integrated que, en una sola unidad, combina las funciones del oxímetro y el pulsímetro y puede ser utilizado con un procesador como Arduino o ESP32. Este sensor basa su funcionamiento en el comportamiento que tiene la sangre ante la luz, esto en función del grado de saturación. 
Para ello el sensor incorpora dos LED ´s una en el espectro infrarrojo (920nm) y el segundo LED rojo (660nm), , un fotodetector, óptica especializada, filtro de luz ambiental entre 50 y 60Hz, y un conversor ADC delta sigma de 16 bits y de hasta 1000 muestras por segundo, además, posee un sensor de temperatura. El sensor se pone al contacto de la piel normalmente el dedo, pero también puede ser puesta en las muñecas y el sensor detecta la luz reflejada, y determina el grado de saturación. 


<img width="921" height="446" alt="image" src="https://github.com/user-attachments/assets/3eb50ebd-7431-418f-ad67-2c6b75ccfabb" />
Https://www.analog.com/en/products/max30102.html

El sensor fue empleado junto con una ESP32, que se encargó de recibir la información del MAX30102 y enviarla al ordenador para ser procesada más tarde en MATLAB. Para esta práctica, aunque el MAX30102 tiene la capacidad de funcionar con una variedad de configuraciones ópticas, se empleó específicamente la señal infrarroja (IR) para adquirir la PPG que se utilizó para calcular el SPI.


<h1 align="center"><i><b>𝐏𝐚𝐫𝐭𝐞 A 𝐝𝐞𝐥 𝐥𝐚𝐛𝐨𝐫𝐚𝐭𝐨𝐫𝐢𝐨</b></i></h1>
𝙈𝙤𝙣𝙩𝙖𝙟𝙚 𝙚𝙭𝙥𝙚𝙧𝙞𝙢𝙚𝙣𝙩𝙖𝙡

<h1 align="center"><i><b>𝐏𝐚𝐫𝐭𝐞 B 𝐝𝐞𝐥 𝐥𝐚𝐛𝐨𝐫𝐚𝐭𝐨𝐫𝐢𝐨</b></i></h1>
# Codigo usado
>
```
%% =========================================================
%  LABORATORIO 3 - SPI CON MAX30102 + ESP32
%  Instrumentacion Biomedica y Biosensores
%
%  Adquisicion:
%       0 - 40 s   : Etapa 1
%      40 - 80 s   : Etapa 2
%      80 - 120 s  : Etapa 3
%
%  Señal utilizada: PPG IR
%% =========================================================

clear;
clc;
close all;

%% =========================================================
% 1. CONFIGURACION
%% =========================================================

puerto = "COM7";       % <-- CAMBIAR POR EL COM DE TU ESP32
baudrate = 115200;

Fs = 100;              % Frecuencia de muestreo [Hz]
duracion = 120;        % Duracion total [s]

N = Fs * duracion;

fprintf('=========================================\n');
fprintf('      SISTEMA DE ADQUISICION SPI\n');
fprintf('=========================================\n\n');

%% =========================================================
% 2. CONEXION CON ESP32
%% =========================================================

try
    s = serialport(puerto, baudrate);
    configureTerminator(s, "LF");
    flush(s);

    fprintf('ESP32 conectado correctamente.\n');

catch
    error(['No se pudo conectar con el ESP32. ', ...
           'Verifica el puerto COM.']);
end

pause(2);

fprintf('\n-----------------------------------------\n');
fprintf('PREPARACION\n');
fprintf('-----------------------------------------\n');
fprintf('Coloca el dedo sobre el MAX30102.\n');
fprintf('Mantén el dedo estable durante toda la captura.\n');
fprintf('La adquisición comenzará en 3 segundos.\n');

pause(3);

%% =========================================================
% 3. ADQUISICION DE LA SEÑAL
%% =========================================================

fprintf('\nADQUISICION INICIADA\n');

senal = zeros(N,1);
tiempo = (0:N-1)'/Fs;

contador = 0;

for k = 1:N

    try
        dato = readline(s);
        valor = str2double(strtrim(dato));

        if isnan(valor)
            valor = 0;
        end

        senal(k) = valor;

    catch
        senal(k) = 0;
    end

    % Mostrar progreso
    if mod(k,Fs*10) == 0
        contador = contador + 10;
        fprintf('Tiempo: %3d / 120 segundos\n', contador);
    end
end

clear s;

fprintf('\nADQUISICION FINALIZADA.\n');

%% =========================================================
% 4. ELIMINAR DATOS INVALIDOS
%% =========================================================

% Interpolar posibles ceros
indiceValido = senal ~= 0;

if sum(indiceValido) < 0.8*N
    warning('Hay demasiadas muestras invalidas.');
end

senal(~indiceValido) = NaN;

senal = fillmissing(senal,'linear');
senal = fillmissing(senal,'nearest');

%% =========================================================
% 5. SEÑAL ORIGINAL
%% =========================================================

figure;

plot(tiempo, senal, 'LineWidth', 1);

xlabel('Tiempo (s)');
ylabel('Amplitud IR');
title('Señal PPG original - MAX30102');

grid on;
xlim([0 duracion]);

%% =========================================================
% 6. FILTRO PPG
%% =========================================================

% Banda utilizada para conservar la componente pulsátil
fc_baja = 0.5;
fc_alta = 5;

[b,a] = butter(3, ...
    [fc_baja fc_alta]/(Fs/2), ...
    'bandpass');

ppg = filtfilt(b,a,senal);

%% =========================================================
% 7. GRAFICA PPG FILTRADA
%% =========================================================

figure;

plot(tiempo, ppg, 'LineWidth', 1.2);

xlabel('Tiempo (s)');
ylabel('Amplitud');
title('Señal PPG filtrada');

grid on;
xlim([0 duracion]);

%% =========================================================
% 8. DETECCION DE MAXIMOS
%% =========================================================

% Distancia minima entre pulsos
distanciaMinima = round(0.40*Fs);

% Prominencia minima
prominencia = 0.15*std(ppg);

[picos, locPicos] = findpeaks( ...
    ppg, ...
    'MinPeakDistance', distanciaMinima, ...
    'MinPeakProminence', prominencia);

tiempoPicos = tiempo(locPicos);

fprintf('\n-----------------------------------------\n');
fprintf('DETECCION DE PULSOS\n');
fprintf('-----------------------------------------\n');

fprintf('Pulsos detectados: %d\n', length(picos));

%% =========================================================
% 9. DETECCION DE MINIMOS
%% =========================================================

[minNegativos, locMinimos] = findpeaks( ...
    -ppg, ...
    'MinPeakDistance', round(0.30*Fs), ...
    'MinPeakProminence', 0.05*std(ppg));

minimos = -minNegativos;
tiempoMinimos = tiempo(locMinimos);

%% =========================================================
% 10. GRAFICA DE MAXIMOS Y MINIMOS
%% =========================================================

figure;

plot(tiempo, ppg, 'LineWidth', 1);
hold on;

plot(tiempoPicos, picos, 'o', ...
    'MarkerSize', 6, ...
    'LineWidth', 1.5);

plot(tiempoMinimos, minimos, 'v', ...
    'MarkerSize', 5, ...
    'LineWidth', 1.2);

xlabel('Tiempo (s)');
ylabel('Amplitud');
title('Detección de máximos y mínimos de la PPG');

legend('PPG','Máximos','Mínimos');

grid on;
xlim([0 duracion]);

%% =========================================================
% 11. CALCULO DEL HBI
% =========================================================

% Intervalo entre pulsaciones
HBI = diff(tiempoPicos);

% Convertir a milisegundos
HBI_ms = HBI * 1000;

% Tiempo asociado a cada HBI
tiempoHBI = tiempoPicos(2:end);

%% =========================================================
% 12. FRECUENCIA CARDIACA
% =========================================================

FC = 60 ./ HBI;

fprintf('\n-----------------------------------------\n');
fprintf('FRECUENCIA CARDIACA\n');
fprintf('-----------------------------------------\n');

fprintf('FC promedio: %.2f BPM\n', mean(FC));

%% =========================================================
% 13. CALCULO DE PPGA
%     Amplitud pico - minimo anterior
%% =========================================================

PPGA = nan(length(picos),1);

for i = 1:length(picos)

    % Buscar minimos anteriores al pico
    candidatos = find(locMinimos < locPicos(i));

    if ~isempty(candidatos)

        indice = candidatos(end);

        % Amplitud pico - valle
        PPGA(i) = picos(i) - minimos(indice);

    end
end

%% =========================================================
% 14. ELIMINAR PPGA INVALIDOS
%% =========================================================

valido = ~isnan(PPGA);

PPGA_valido = PPGA(valido);
tiempoPPGA = tiempoPicos(valido);

%% =========================================================
% 15. HBI Y PPGA DE LONGITUD COMPATIBLE
%% =========================================================

% HBI corresponde a pulsos desde el segundo pulso
% Buscamos hacer coincidir ambos parametros

tiempoSPI = tiempoHBI;

PPGA_SPI = nan(length(HBI),1);

for i = 1:length(HBI)

    % Buscar PPGA correspondiente al segundo pulso
    [~, indice] = min(abs(tiempoPPGA - tiempoSPI(i)));

    PPGA_SPI(i) = PPGA_valido(indice);

end

%% =========================================================
% 16. ELIMINAR VALORES EXTREMOS
%% =========================================================

validoSPI = ...
    HBI_ms > 300 & ...
    HBI_ms < 1500 & ...
    PPGA_SPI > 0;

HBI_SPI = HBI_ms(validoSPI);
PPGA_SPI = PPGA_SPI(validoSPI);
tiempoSPI = tiempoSPI(validoSPI);

%% =========================================================
% 17. NORMALIZACION
%
% El SPI utiliza HBI normalizado y PPGA normalizado.
%
% Para esta practica se realiza una normalizacion
% min-max sobre la captura obtenida.
%% =========================================================

HBI_min = min(HBI_SPI);
HBI_max = max(HBI_SPI);

PPGA_min = min(PPGA_SPI);
PPGA_max = max(PPGA_SPI);

HBI_norm = ...
    100 * (HBI_SPI - HBI_min) / ...
    (HBI_max - HBI_min);

PPGA_norm = ...
    100 * (PPGA_SPI - PPGA_min) / ...
    (PPGA_max - PPGA_min);

%% =========================================================
% 18. CALCULO DEL SPI
%
% SPI = 100 - (0.7*PPGA_norm + 0.3*HBI_norm)
%% =========================================================

SPI = 100 - ...
    (0.7*PPGA_norm + 0.3*HBI_norm);

%% =========================================================
% 19. LIMITAR SPI ENTRE 0 Y 100
%% =========================================================

SPI(SPI < 0) = 0;
SPI(SPI > 100) = 100;

%% =========================================================
% 20. SUAVIZADO DEL SPI
%% =========================================================

% Aproximadamente 15 segundos
ventanaSPI = max(3,round(15/median(HBI_SPI/1000)));

SPI_suave = movmedian(SPI, ventanaSPI);

%% =========================================================
% 21. GRAFICA DEL SPI
%% =========================================================

figure;

plot(tiempoSPI, SPI, '.', ...
    'MarkerSize', 10);

hold on;

plot(tiempoSPI, SPI_suave, ...
    'LineWidth', 2);

xlabel('Tiempo (s)');
ylabel('SPI');
title('Evolución del Surgical Pleth Index');

legend('SPI por latido','SPI suavizado');

grid on;

ylim([0 100]);
xlim([0 120]);

%% =========================================================
% 22. LINEAS DE LAS TRES ETAPAS
%% =========================================================

xline(40,'--','40 s');
xline(80,'--','80 s');

%% =========================================================
% 23. CALCULO POR ETAPAS
%% =========================================================

etapa1 = tiempoSPI < 40;

etapa2 = tiempoSPI >= 40 & tiempoSPI < 80;

etapa3 = tiempoSPI >= 80;

SPI1 = SPI_suave(etapa1);
SPI2 = SPI_suave(etapa2);
SPI3 = SPI_suave(etapa3);

fprintf('\n=========================================\n');
fprintf('             RESULTADOS SPI\n');
fprintf('=========================================\n');

fprintf('\nEtapa 1 (0-40 s):\n');
fprintf('SPI promedio = %.2f\n',mean(SPI1,'omitnan'));

fprintf('\nEtapa 2 (40-80 s):\n');
fprintf('SPI promedio = %.2f\n',mean(SPI2,'omitnan'));

fprintf('\nEtapa 3 (80-120 s):\n');
fprintf('SPI promedio = %.2f\n',mean(SPI3,'omitnan'));

%% =========================================================
% 24. TABLA DE RESULTADOS
%% =========================================================

Resultados = table( ...
    tiempoSPI, ...
    HBI_SPI, ...
    PPGA_SPI, ...
    HBI_norm, ...
    PPGA_norm, ...
    SPI, ...
    SPI_suave, ...
    'VariableNames', ...
    {'Tiempo_s','HBI_ms','PPGA', ...
     'HBI_norm','PPGA_norm','SPI','SPI_suave'});

disp(Resultados);

%% =========================================================
% 25. GUARDAR RESULTADOS
%% =========================================================

writetable(Resultados,'Resultados_SPI_MAX30102.csv');

save('Datos_SPI_MAX30102.mat', ...
    'tiempo', ...
    'senal', ...
    'ppg', ...
    'tiempoPicos', ...
    'picos', ...
    'tiempoMinimos', ...
    'minimos', ...
    'HBI_SPI', ...
    'PPGA_SPI', ...
    'SPI', ...
    'SPI_suave');

fprintf('\n-----------------------------------------\n');
fprintf('Archivos guardados:\n');
fprintf('Resultados_SPI_MAX30102.csv\n');
fprintf('Datos_SPI_MAX30102.mat\n');
fprintf('-----------------------------------------\n');

<h1 align="center"><i><b>𝐏𝐚𝐫𝐭𝐞 C 𝐝𝐞𝐥 𝐥𝐚𝐛𝐨𝐫𝐚𝐭𝐨𝐫𝐢𝐨</b></i></h1>

<h2 align="center">Resultados</h2>


El codigo adquiere 120 segundos de senal PPG (canal IR) del sensor MAX30102 a traves de un ESP32, muestreada a 100 Hz y dividida en tres etapas de 40 segundos cada una. Sobre la senal cruda se realiza limpieza de muestras invalidas (interpolacion lineal), filtrado pasa-banda Butterworth de orden 3 (0.5-5 Hz) para aislar la componente pulsatil, deteccion de maximos y minimos por latido, calculo del intervalo entre latidos (HBI) y de la amplitud de pulso (PPGA), normalizacion min-max de ambos parametros y combinacion en el Surgical Pleth Index mediante la formula SPI = 100 - (0.7*PPGA_norm + 0.3*HBI_norm), limitado al rango 0-100 y suavizado con una mediana movil de aproximadamente 15 segundos.
A continuacion se presentan los resultados obtenidos en cada etapa del procesamiento, junto con el analisis de las graficas generadas por el script.

*Senal PPG original*

 <img width="875" height="469" alt="image" src="https://github.com/user-attachments/assets/b73349eb-2a0c-4020-a2a7-5ce1d7627bec" />

Figura 1. Senal PPG original (IR) adquirida del MAX30102, 0-120 s.

La senal cruda oscila aproximadamente entre 1.17x10^5 y 1.27x10^5 unidades y esta dominada por una componente lenta de baja frecuencia (deriva de linea base), sin pulsatilidad cardiaca visible a esta escala. Se identifican tres tramos con mayor variabilidad y pendientes pronunciadas, aproximadamente entre 20-40 s, 55-75 s y 85-100 s, que coinciden con los cambios de mayor amplitud en la curva. Estos tramos son compatibles con artefactos de movimiento o variacion en la presion de contacto del dedo sobre el sensor, mas que con actividad cardiaca.

*Senal PPG filtrada*

 <img width="875" height="406" alt="image" src="https://github.com/user-attachments/assets/e81c18b8-d389-4556-b58f-a9863888dd8a" />
 

Figura 2. Senal PPG filtrada (0.5-5 Hz) completa (arriba) y ventana ampliada 45-55 s (abajo).

Tras el filtrado pasa-banda se recupera la componente pulsatil de la senal. En la ventana ampliada (45-55 s) se observa un patron cuasi periodico con ciclos de aproximadamente 0.8-1.0 s, equivalentes a una frecuencia cardiaca del orden de 60-75 lpm, con morfologia de pulso reconocible (ascenso sistolico y componente dicroto) en varios ciclos consecutivos.
En la vista completa se identifican ademas varias ráfagas de amplitud notablemente mayor al resto del trazado (picos cercanos a 900 y valles cercanos a -1200 alrededor de los 20, 40, 75 y 115 s), muy superiores a la amplitud tipica del pulso (aprox. +-200 a 300). Estas ráfagas coinciden temporalmente con los tramos de mayor variabilidad ya identificados en la senal original, lo que confirma que corresponden a artefactos de movimiento que sobreviven al filtrado, y no a variaciones fisiologicas reales del pulso.

*Deteccion de maximos y minimos*

 <img width="875" height="469" alt="image" src="https://github.com/user-attachments/assets/bd24b39c-1759-437f-bcf2-f89b30a162a3" />
 
Figura 3. Deteccion de maximos y minimos sobre la PPG filtrada, ventana 38-48 s.

El algoritmo findpeaks localiza correctamente un maximo y un minimo por ciclo en la mayor parte del registro mostrado. No obstante, en el tramo cercano a 39.5-40 s, coincidiendo con el artefacto de gran amplitud, se detectan multiples maximos y minimos muy proximos entre si (menos de 1 segundo de separacion), lo que equivale a contabilizar 2 a 3 'latidos' donde en realidad ocurre un unico evento de artefacto. Este comportamiento se debe a que la distancia minima entre picos (0.40 s, equivalente a un limite de 150 lpm) y la prominencia minima (calculada como una fraccion del std global de toda la senal) no son suficientemente restrictivas frente a un artefacto de gran amplitud.
2.4 Evolucion del Surgical Pleth Index (SPI)

 <img width="875" height="469" alt="image" src="https://github.com/user-attachments/assets/d862da20-00ae-41fc-a4e5-01e76cbc5f08" />
 
Figura 4. Evolucion del SPI por latido (puntos) y SPI suavizado con mediana movil (linea), con division en las tres etapas de 40 s.

El SPI calculado latido a latido presenta una dispersion muy amplia a lo largo de todo el registro, con valores puntuales que van desde menos de 5 hasta mas de 90, reflejo directo de los latidos mal detectados sobre los artefactos descritos en la seccion 2.3. La curva suavizada (mediana movil ~15 s) atenua ese ruido y permite observar una tendencia mas clara por etapa, resumida en la Tabla 1.
Tabla 1. Tendencia del SPI suavizado por etapa (lectura aproximada de la Figura 4).
Etapa	Intervalo (s)	SPI promedio (aprox.)	Tendencia
Etapa 1	0 - 40	~80 -> ~55	Descenso progresivo
Etapa 2	40 - 80	~55 - 70 (fluctuante)	Inestable, sin tendencia clara
Etapa 3	80 - 120	~70 -> ~52	Descenso sostenido

En la Etapa 1 (0-40 s) el SPI suavizado inicia en su valor mas alto de todo el registro (~80) y desciende de forma progresiva hasta estabilizarse cerca de 54-55 hacia el final de la etapa. En la Etapa 2 (40-80 s) el indice se recupera parcialmente al inicio (~65-70) pero fluctua de manera inestable durante el resto del intervalo, sin una tendencia neta clara, incluyendo un descenso transitorio alrededor de los 55-60 s. En la Etapa 3 (80-120 s) el SPI suavizado alcanza nuevamente un valor alto al comienzo (~70, el segundo mas alto del registro) y despues desciende de forma sostenida y consistente hasta valores cercanos a 52-55, manteniendose estable en el tramo final.

<h2 align="center">Analisis</h2>


Calidad de la senal adquirida
La senal original no muestra pulsatilidad visible a simple vista, lo cual es normal para una senal PPG cruda dominada por su componente DC y por variaciones lentas de perfusion o de contacto del sensor; el filtro pasa-banda es indispensable y cumple su funcion de exponer el componente cardiaco. Sin embargo, los tramos de alta variabilidad detectados en la senal cruda (aprox. 20-40 s, 55-75 s y 85-100 s) se traducen directamente en las ráfagas de gran amplitud observadas en la senal filtrada, lo que indica que el filtrado por si solo no es suficiente para eliminar artefactos de movimiento cuyo contenido espectral se superpone con la banda de interes (0.5-5 Hz).

Robustez de la deteccion de picos
El criterio de deteccion de maximos y minimos usa una distancia minima fija y una prominencia minima basada en el std global de toda la senal filtrada. Ambos criterios son razonables para una senal PPG limpia, pero resultan insuficientes cuando existen artefactos de gran amplitud: la distancia minima de 0.40 s permite frecuencias cardiacas de hasta 150 lpm, por lo que un artefacto oscilante puede generar varios falsos picos dentro de ese margen; y al calcularse la prominencia sobre el std de toda la senal, un artefacto de gran amplitud puede tanto generar picos que superen facilmente ese umbral como sesgar el umbral aplicado al resto del registro. El resultado visible en la Figura 3 (multiples picos muy proximos cerca de los 39.5-40 s) confirma esta limitacion.

Impacto sobre el HBI, el PPGA y el SPI
Como el HBI y el PPGA se calculan directamente a partir de los maximos y minimos detectados, los falsos picos originados en los artefactos generan intervalos entre latidos y amplitudes de pulso que no corresponden a eventos cardiacos reales. Estos valores atipicos, aunque se filtran parcialmente con los limites fisiologicos aplicados en el paso 16 del codigo (HBI entre 300 y 1500 ms, PPGA positivo), no son eliminados en su totalidad, y ademas afectan la normalizacion min-max de HBI y PPGA, que se calcula sobre todo el registro: un unico artefacto extremo puede comprimir la escala normalizada del resto de los latidos validos. Esto explica la alta dispersion del SPI latido a latido observada en la Figura 4, y motiva el uso del suavizado por mediana movil como paso necesario para obtener una tendencia interpretable.

Interpretacion fisiologica de la tendencia por etapas
Considerando unicamente la curva suavizada, el patron dominante es un SPI alto al inicio de las etapas 1 y 3, con descenso progresivo hacia el final de cada una, y un comportamiento mas inestable en la etapa 2. Dado que el SPI se construye para disminuir cuando aumenta el tono simpatico (menor amplitud de pulso PPGA y menor variabilidad HBI, asociados a vasoconstriccion y mayor actividad simpatica), la tendencia descendente en las etapas 1 y 3 es compatible con un incremento progresivo del nivel de estres, dolor o activacion simpatica del sujeto a lo largo de cada intervalo, mientras que la etapa 2 no permite una conclusion tan clara debido a su mayor inestabilidad, posiblemente relacionada con los artefactos identificados en esa franja horaria (55-75 s).

Limitaciones y recomendaciones
●	Implementar un umbral de prominencia adaptativo (por ventana local, no sobre el std global) para que los artefactos de una zona no afecten la sensibilidad de deteccion en el resto del registro.
●	Incorporar un indice de calidad de senal (SQI) que permita marcar o descartar los segmentos con artefacto antes de calcular HBI, PPGA y SPI, en lugar de solo aplicar limites fisiologicos posteriores.
●	Reducir el limite superior de frecuencia cardiaca admitido (MinPeakDistance) a un valor mas conservador si se conoce el rango fisiologico esperado del sujeto, para evitar la deteccion de picos dobles sobre artefactos.
●	Normalizar HBI y PPGA por ventanas moviles en vez de sobre el registro completo, para que un artefacto puntual no distorsione la escala usada en el resto de la captura.
●	Repetir la adquisicion solicitando explicitamente inmovilidad del dedo durante los segundos identificados como criticos, o repetir el procesamiento excluyendo manualmente esos tramos, para contrastar la tendencia del SPI obtenida


𝘾𝙤𝙣𝙘𝙡𝙪𝙨𝙞𝙤𝙣

𝘽𝙞𝙗𝙡𝙞𝙤𝙜𝙧𝙖𝙛𝙞𝙖
Llamas, L. (2020, 12 abril). Pulsímetro y oxímetro con Arduino y MAX30102. Luis Llamas. https://www.luisllamas.es/pulsimetro-y-oximetro-con-arduino-y-max30102/
MAX30102 - Sensor de concentración de Oxígeno y Ritmo cardíaco - Electronilab. (2026, 16 septiembre). Electronilab. https://electronilab.co/tienda/max30100-sensor-de-concentracion-de-oxigeno-y-ritmo-cardiaco/



