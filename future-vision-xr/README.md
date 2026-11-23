# FUTURE VISION XR 2.0 — Interactive Classroom Edition

Web app educativa para exposición de realidad aumentada. Proyecto Vite + Three.js + MediaPipe Hand Landmarker + jsQR + Supabase Realtime opcional.

## Incluye
- Interfaz responsive para móviles, tablets y escritorio.
- Cámara trasera mediante `getUserMedia` (requiere HTTPS o localhost).
- Renderizado 3D con seis módulos de demostración creados proceduralmente.
- Seguimiento de hasta dos manos con MediaPipe.
- Pellizco para mover, dos manos para escalar y puño para desmontar/ensamblar.
- Modo libre y detección de QR en el video.
- Audio de pronunciación en inglés mediante SpeechSynthesis.
- Sala compartida opcional mediante Supabase Realtime Broadcast.
- Controles táctiles alternativos.

## Importante: alcance técnico real
Esta entrega es un MVP funcional de cámara + overlay 3D. El modo libre coloca los modelos en coordenadas de pantalla y el modo QR usa el centro del QR detectado como referencia 2D. **No es todavía un anclaje espacial 6DoF**: al mover el teléfono, no se calcula la pose física completa de la mesa ni se mantiene un ancla mundial persistente. Para conseguirlo se debe integrar un motor WebAR con image tracking/pose tracking (por ejemplo, MindAR con un target compilado) o una app nativa ARKit/ARCore. WebXR immersive-ar no está disponible en Safari iOS/iPadOS, por lo que no se debe usar como única vía multiplataforma.

Los modelos incluidos son geometría procedural demostrativa, no escaneos fotorrealistas ni modelos anatómicos/mecánicos de precisión. Se pueden sustituir por archivos GLB licenciados; ver sección Modelos 3D.

## Requisitos
- Node.js 20.19+ o 22.12+ recomendado para Vite 7.
- Navegador moderno con WebAssembly, WebGL y cámara.
- HTTPS en producción para cámara.

## Ejecutar localmente
```bash
npm install
npm run dev
```
Abre la URL HTTPS/LAN que corresponda. Para pruebas de cámara en un teléfono, usa un túnel HTTPS o despliega primero en Vercel; `localhost` es seguro solo en el propio dispositivo.

## Build
```bash
npm run build
npm run preview
```

## Publicar en GitHub y Vercel
1. Crea un repositorio GitHub llamado `future-vision-xr-2`.
2. Sube todos los archivos de esta carpeta.
3. En Vercel, importa el repositorio.
4. Framework preset: **Vite**.
5. Build command: `npm run build`.
6. Output directory: `dist`.
7. Deploy.

También puedes desplegar desde CLI:
```bash
npm i -g vercel
vercel
vercel --prod
```

## Activar sesión compartida (Supabase)
1. Crea un proyecto Supabase.
2. Copia Project URL y la publishable key (o anon key compatible) desde el panel.
3. En Vercel → Project → Settings → Environment Variables agrega:
   - `VITE_SUPABASE_URL`
   - `VITE_SUPABASE_ANON_KEY`
4. Haz redeploy.
5. Todos los participantes introducen el mismo código de sala y pulsan **Unirse**.

La clave pública del cliente no debe ser una `service_role` ni una secret key. El canal usado por esta demo es público por nombre de sala: no transmitir datos personales ni secretos. Para eventos privados se debe implementar autenticación y políticas Realtime Authorization.

## Gestos
- Pinch (pulgar + índice): arrastrar el objeto.
- Dos manos: cambiar escala aproximando/separando las manos.
- Puño: alternar desmontaje/ensamblaje.
- Tocar el canvas: posicionamiento alternativo.

## Marcador QR
El modo QR detecta cualquier QR visible y coloca el modelo cerca de su centro en el plano de pantalla. Para una demo controlada, imprime un QR grande con el código de la sala o cualquier URL. La detección QR es un punto de referencia 2D, no un marcador de pose 6DoF.

## Modelos 3D realistas
Los seis modelos actuales son prototipos procedurales ligeros. Para reemplazarlos, exporta modelos `.glb` optimizados (idealmente menos de 8–12 MB por modelo, texturas comprimidas y geometría razonable) desde Blender o descárgalos de Sketchfab, Poly Pizza, CGTrader o NASA 3D Resources. Revisa siempre la licencia y atribución. La siguiente iteración debe añadir `GLTFLoader` y manifiesto de modelos GLB.

## Privacidad y rendimiento
- El video se procesa en el navegador y no se sube al servidor por este código.
- MediaPipe descarga su runtime/modelo desde CDN la primera vez.
- Cierra la cámara al abandonar la experiencia.
- En móviles de gama baja reduce resolución/FPS y desactiva sombras pesadas.
- Para compartir estado se envían transformaciones y eventos, nunca el video.

## Estructura
```text
future-vision-xr/
├── index.html
├── package.json
├── vercel.json
├── .env.example
├── README.md
└── src/
    ├── main.js
    └── style.css
```
