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
```
>

<h1 align="center"><i><b>𝐏𝐚𝐫𝐭𝐞 C 𝐝𝐞𝐥 𝐥𝐚𝐛𝐨𝐫𝐚𝐭𝐨𝐫𝐢𝐨</b></i></h1>

**Resultados**

𝘾𝙤𝙣𝙘𝙡𝙪𝙨𝙞𝙤𝙣

𝘽𝙞𝙗𝙡𝙞𝙤𝙜𝙧𝙖𝙛𝙞𝙖
Llamas, L. (2020, 12 abril). Pulsímetro y oxímetro con Arduino y MAX30102. Luis Llamas. https://www.luisllamas.es/pulsimetro-y-oximetro-con-arduino-y-max30102/
MAX30102 - Sensor de concentración de Oxígeno y Ritmo cardíaco - Electronilab. (2026, 16 septiembre). Electronilab. https://electronilab.co/tienda/max30100-sensor-de-concentracion-de-oxigeno-y-ritmo-cardiaco/

