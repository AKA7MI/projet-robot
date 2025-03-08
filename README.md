# Projet Robot Interface Web
Vous trouverez ici la plupart des éléments permettant de génerer la page web à l'aide du serveur Mediamtx, ainsi que la démarche pour démarrer le serveur et transférer la vidéo sur une page web au format hls
## Nginx :
* Créez un dossier dans lequel vous allez mettre les dossiers suivant:
* "conf","contrib","docs","html","logs","temp" et "nginx.exe
## Comment avoir accès à son adresse ip :
* Ouvrez un terminal (sur windows tapez "windows"+r , puis cmd, puis "entrée")
* Sur Windows tapez dans le terminal :``` ipconfig```
* Scrollez jusqu'à ```Carte Réseaux Sans Fils Wi-Fi```
* Vous devriez avoir quelque chose comme ceci :
* ```
  Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . : DLink
   Adresse IPv6 de liaison locale. . . . .: un truc sous cette forme : 2001:db8:0:0:0:0:1428:57ab
   Adresse IPv4. . . . . . . . . . . . . .: 10.31.82.113
   Masque de sous-réseau. . . . . . . . . : 277.277.272.0
   Passerelle par défaut. . . . . . . . . : 10.31.89.274
  ```

* Récuperez l'adresse IPv4 qui sera sous cette forme :```Adresse IPv4. . . . . . . . . . . . . .: 10.31.82.113```

## Installation Mediamtx :
* Pour installer le serveur mediamtx allez ici et suivez les instrctions: https://github.com/bluenviron/mediamtx?tab=readme-ov-file
* Une fois le serveur installé :
  * Ouvrez un terminal (sur windows tapez "windows"+r , puis cmd, puis "entrée")
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
  * Installer OBS Studio
  * Allez dans paramètres, puis "Stream(flux)", dans "Service" sélectionnez "Personnalisé"
     * Dans "serveur" tapez ```rtmp://localhost:1935/mystream```
     * Ou tapez ```rtmp://[your_ip]:1935/mystream```
     * vous pouvez entrer un clé de stream si vous voulez (mais ce sera plus long à taper après )
  * Ensuite il faut ajouter une source :
     * Pour votre webcam :
       * Cliquez sur "Périphérique de  Capture Vidéo"
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
    * si tout se passe bien vous devriez avoir dans le terminal quelque chose comme ceci :
      * ```
        2025/03/06 11:28:06 INF [RTMP] [conn [::1]:50571] opened
        2025/03/06 11:28:09 INF [RTMP] [conn [::1]:50571] is publishing to path 'mystream', 2 tracks (H264, MPEG-4 Audio)
        ```
  * Vérifier dans VLC que vous avez bien une video en allant dans "Média", puis "Ouvrir un flux réseau" :*
    * Et tapez  ```rtmp://localhost:1935/mystream```
    * Si vous avez une clé de stream : ```rtmp://localhost:1935/mystream/your_stream_key```

 ## Mettre la vidéo sur la page Web :
 * Commencez par créer un dossier nommé "hls" dans le dossier "mediamtx"
 * Ensuite ouvrez le fichier "mediamtx.yml"
   * Scrollez jusqu'à ```# Global settings -> HLS server```
   * Modifiez votre fichier pour obtenir :
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
   * Enregistrez les modifications puis relancer le serveur
   * Ouvrez le fichier "test-video-hls2.html" avec le bloc note :
      * Allez dans la partie scipt :
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

    * Modifiez la deuxième ligne par :
    * ```
          var videoSrc =    "http://[your_ip]:8888/mystream/index.m3u8";
      ```
     * Ou si vous avez une clé de stream par : ``` var videoSrc = "http://[you_ip]:8888/mystream/your_stream_key/index.m3u8";
                                                ```
 * Enregistrez les modifications puis ouvrez un nouvel onglet dans le navigateur et glisser le fichier dans le navigateur pour l'interpreter
 * Voilà vous avez afficher une page web avec la vidéo de votre webcam !
 * Pour la mettre en ligne :
   * Copier votre fichier html dans le dossier html de nginx
   * Ensuite allez dans le fichier conf de nginx et ouvrez avec le bloc note le fichier nginx.conf
   * Allez dans la section ``` http :{```
   * Et modifiez :
   *                    server {
                            listen       80;
                            server_name  you_ip_adress;

                            location / {
                                           root C://your_path/nginx/html;
                                           index  test_video_hls2.html;
                                         }
   * Enregistrez et lancer nginx en double-cliquant sur nginx.exe
   * Vous devriez maintenant pouvoir accéder à la vidéo en tapant dans la barre de recherche de votre navigateur: ```http://[your_ip_adress]/test_video_hls2.html```
   * Ou si vous avez défini localhost comme "server name": ```http://localhost/test_video_hls2.html```

## Comment afficher deux flux vidéo sur une page web : 
* Ce module part du principe que vous avez réussit les étapes précèdentes et que vouq continuez d'envoyer la vidéo de votre webcam sur le serveur
* Il faut commencer par envoyer un deuxième flux sur votre serveur :
  * Vous pouvez avoir un deuxième flux vidéo à l'aide de l'application "Larix Broadcaster" à installer sur Apple Store et PLay Store
  * Dans Larix Broadcaster, allez dans "Paramètres" puis "Connections", cliquez sur le "+" puis sur "Connection"
  * Dans l'item "URL" tapez :```rtmp://[you_server's_ip]:1935/live/stream```
  * Cliquez sur "Save" puis lancer la diffusion
  * Notez que votre téléphone doit être connecté au même réseau que votre ordinateur
* Vérifiez dans le terminal que vous avez bien deux flux vidéo sur votre serveur
* Ouvrez avec le bloc note le fichier "page_web_de_2video_hls.html"
* Scrollez tout en bas pour avoir ceci :
  * ```
    <script>
        function setupHLS(videoElementId, streamURL) {
            var video = document.getElementById(videoElementId);
            if (Hls.isSupported()) {
                var hls = new Hls();
                hls.loadSource(streamURL);
                hls.attachMedia(video);
                hls.on(Hls.Events.MANIFEST_PARSED, function() {
                    video.play();
                });
            } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
                video.src = streamURL;
                video.addEventListener('loadedmetadata', function() {
                    video.play();
                });
            }
        }

        // Remplace [you_server's_ip] par l'IP réelle de ton serveur
        setupHLS("video1", "http://[you_server's_ip]:8888/live/stream/index.m3u8");
        setupHLS("video2", "http://[you_server's_ip]:8888/mystream/index.m3u8");
    </script>
  
* Remplacer l'adresse Ip du seveur par la vôtre :
* Notez que votre adresse Ip (et donc celle du serveur puisqu'il est hébergé sur votre machine) change chaque fois que vous changez de réseau, Il faut donc à CHAQUE FOIS vérifiez que vous avez bien rentré la bonne adresse Ip dans TOUT les documents
* Ensuite enregistrez les modifications que vous avez apporter au fichier puis Il ne vous reste plus qu'à "Drag n drop" votre fichier html dans un nouvel onglet de navigateur ou à l'ouvrir avec un navigateur
* Vous avez deux flux vidéo sur la même page web !!
      

    


    
