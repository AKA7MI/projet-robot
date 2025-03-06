# projet-robot-interface-web
Vous trouverez ici la plupart des élémennts permettant de génerer la page web à l'aide du serveur mediamtx, ainsi que la demarche pour démarrer le serveur et transférer la video sur une page web au format hls
## Installation Mediamtx :
* Pour installer le serveur mediamtx allez ici et suivez les instrctions: https://github.com/bluenviron/mediamtx?tab=readme-ov-file
* Une fois le serveur installé :
  * ouvrez un terminal (sur windows tapez "windows"+r , puis cmd, puis "entrée")
  * Sur Windows: ``` cd C://le-chemin/du-dossier/où-est-mediamtx.exe ```
  * Pour démarrer le serveur tapez dans le terminal :  ```mediamtx.exe ```
  * Vous devriez voir quelque chose du style :
       * ```
             2025/03/06 10:48:25 INF MediaMTX v1.11.3
             2025/03/06 10:48:25 INF configuration loaded from C:\Users\brune\OneDrive\Bureau\projet_robot_page_web\mediamtx\mediamtx.yml
             2025/03/06 10:48:25 INF [path relay] [RTMP source] started
             2025/03/06 10:48:25 INF [RTSP] listener opened on :8554 (TCP), :8000 (UDP/RTP), :8001 (UDP/RTCP)
             2025/03/06 10:48:25 INF [RTMP] listener opened on :1935
             2025/03/06 10:48:25 INF [HLS] listener opened on :8888
             2025/03/06 10:48:25 INF [WebRTC] listener opened on :8889 (HTTP), :8189 (ICE/UDP)
             2025/03/06 10:48:25 INF [SRT] listener opened on :8890 (UDP)
         ```
## Flux vidéo sur le serveur :
* Si vous voulez envoyer le flux video de votre webcam sur le serveur :
  * installer OBS Studio
  * allez dans paramètres, puis "Stream(flux)", dans "Service" sélectionnez "Personnalisé"
     * Dans "serveur" tapez ```rtmp://localhost:1935/mystream```
     * ou tapez ```rtmp://[your_ip]:1935/mystream```
     * vous pouvez entrer un clé de stream si vous voulez (mais ce sera plus long à taper après )
  * Ensuite il faut ajouter une source :
     * Pour votre webcam :
       * cliquez sur "Périphérique de  Capture Vidéo"
       * Puis "OK"
       * Puis "Integrated Camera"
       * Validez
  * Dans le dossier "Mediamtx" vous trouverez un fichier nommé "mediamtx.yml", C'est le panneaux de configuration de votre serveur :
    * Scrollez tout en bas et tapez ``` paths :
                                            all_others : ```
    * Vous êtes prêts à lancer votre premier stream !
  * Il ne vous reste plus qu'à cliquer sur "commencer le stream", vous diffusez votre video sur le serveur !
    * si tout se passe bien vous devriez avoir dans le terminal quelque chose comme :
      * ```
        2025/03/06 11:28:06 INF [RTMP] [conn [::1]:50571] opened
        2025/03/06 11:28:09 INF [RTMP] [conn [::1]:50571] is publishing to path 'mystream', 2 tracks (H264, MPEG-4 Audio)
        ```
  * Vérifier dans VLC que vous avez bien une video en allant dans "Média", puis "Ouvrir un flux réseau" :*
    * et tapez  ```rtmp://localhost:1935/mystream```
    * si vous avez une clé de stream : ```rtmp://localhost:1935/mystream/your_stream_key```

 ## Mettre la vidéo sur la page Web :
 * Commencez par créer un dossier nommé "hls" dans le dossier "mediamtx"
 * Ensuite ouvrez le dossier 

    


    
