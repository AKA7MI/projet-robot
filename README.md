# projet robot interface web
Vous trouverez ici la plupart des élémennts permettant de génerer la page web à l'aide du serveur mediamtx, ainsi que la demarche pour démarrer le serveur et transférer la video sur une page web au format hls
## Nginx :
* créez un dossier dans lequel vous allez mettre les dossiers suivant:
* "conf","contrib","docs","html","logs","temp" et "nginx.exe

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
    * Pour ouvrir le fichier yml, utilisez le bloc note
    * Scrollez tout en bas et tapez ``` paths :
                                            all_others : ```
    * Enregistrez le fichier puis redémarrer le serveur
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
 * Ensuite ouvrez le fichier "mediamtx.yml"
   * Scrollez jusqu'à ```# Global settings -> HLS server```
   * modifiez votre fichier pour obtenir :
     * ```
       hls: yes
       hlsAddress: :8888
       hlsEncryption: no
       hlsServerKey: server.key
       hlsServerCert: server.crt
       hlsAllowOrigin: '*'
       hlsTrustedProxies: []
       hlsAlwaysRemux: yes
       hlsVariant: mpegts
       hlsSegmentCount: 5
       hlsSegmentDuration: 4s
       hlsPartDuration: 200ms
       hlsSegmentMaxSize: 50M
       hlsDirectory: ./hls
       hlsMuxerCloseAfter: 300s
       ```
   * enregistrez les modifications puis relancer le serveur
   * Ouvrez le fichier "test-video-hls2.html" avec le bloc note :
      * dans la partie scipt :
      * ```
        <script>
        var video = document.getElementById('video');
        var videoSrc = "http://10.31.67.234:8888/relay/index.m3u8";

        if (Hls.isSupported()) {
            var hls = new Hls();
            hls.loadSource(videoSrc);
            hls.attachMedia(video);
            hls.on(Hls.Events.MANIFEST_PARSED, function () {
                video.play();
            });
        } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
            video.src = videoSrc;
            video.addEventListener('loadedmetadata', function () {
                video.play();
            });
        }
         </script>
        
      ```
    * Modifiez la deuxième ligne par :
    * ```
       var videoSrc = "http://[your_ip]:8888/mystream/index.m3u8";
      ```
     * ou si vous avez une clé de stream par : ``` var videoSrc = "http://[you_ip]:8888/mystream/your_stream_key/index.m3u8";
                                                ```
 * Enregistrer les modifications puis ouvrez un nouvel onglet dans le navigateur et glisser le fichier dans le navigateur pour l'interpreter
 * Voilà vous avez afficher une page web avec la vidéo de votre webcam !
 * Pour la mettre en ligne :
   * copier votre fichier html dans le dossier html de nginx
   * Ensuite allez dans le fichier conf de nginx et ouvrez avec le bloc note le fichier nginx.conf
   * Allez dans la section ``` http :{```
   * et modifiez : ```
   *                    server {
                            listen       80;
                            server_name  you_ip_adress;

                            location / {
                                           root C://your_path/nginx/html;
                                           index  test_video_hls2.html;
                                         }
   * enregistrez et lancer nginx en double-cliquant sur nginx.exe
   * Vous devriez maintenant pouvoir accéder à la vidéo en tapant dans la barre de recherche de votre navigateur: ```http://[your_ip_adress]/test_video_hls2.html```
   * ou si vous avez défini localhost comme "server name": ```http://localhost/test_video_hls2.html```

      

    


    
