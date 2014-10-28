hi5-bot-friendinviter
=====================
Labels
bot, jamesjara, hi5, hacking, hi5bot, mozilla, xul, JavaScript, urnmozilla

Hi5 bot for send massive invitations running in a mozilla xul extension 

```Javascript

var xmlHttp;

function estadoProgreso(state) {
    document.getElementById('progreso').mode = (state) ? 'undetermined' : 'determined';
};

function eliminarDuplicados(a) {
    var idx = {},
        ai, o = [];
    for (var i = 0, l = a.length; i < l; i++) {
        ai = a[i];
        if (!idx[ai]) {
            o.push(ai);
            idx[ai] = true;
        };
    };
    return o;
};

function goToPost() {
   // if (xmlHttp.readyState == 4 || xmlHttp.readyState == 'complete') {}
};

function GetXmlHttpObject() {
    var xmlHttp = null;
    xmlHttp = new XMLHttpRequest();
    return xmlHttp;
};

function enviarInvitacion(timestamp, jscode, user) {
    xmlHttp = false;
    xmlHttp = GetXmlHttpObject();
    if (xmlHttp.overrideMimeType) {
        xmlHttp.overrideMimeType('text/xml; charset=utf-8');
    };
    if (xmlHttp == null) {
        alert('Esta shit no soporta envio xmlHttp ');
        return;
    };
    var url = 'http://hi5.com/friend/addFriendAjax.do';
    var parameters = 'timestamp=' + timestamp + '&js=' + jscode + '&requestSource=FRIENDS_OF_FRIENDS' + '&userid=' + user + '&userId=' + '';
    xmlHttp.onreadystatechange = goToPost;
    xmlHttp.open('POST', url, true);
    xmlHttp.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');
    xmlHttp.setRequestHeader('Content-length', parameters.length);
    xmlHttp.setRequestHeader('Connection', 'close');
    xmlHttp.send(parameters);
};

//A -> ESCANEAR


function a() {
    estadoProgreso(true);
    var iDsStore = new Array();
    var Documento = window.opener.content.document.body.innerHTML;
    var patron = /friend\/p(.*?)--/gim;
    var patron2 = /displayProfile.do\?userid=(.*?)\"/gim;
    //Buscamos con el metodo 1 /friend\/p(.*?)--/
    var resultado = Documento.match(patron);
    var concidencia;
    if (resultado != null) {
        while ((concidencia = patron.exec(Documento))) {
            estadoProgreso(true);
            iDsStore.push(concidencia[1]);
        };
        if (concidencia == null) {
            estadoProgreso(false);
        };
    } else {
        alert('En esta pagina no se encontraron Perfiles(IDS-1) de Hi5');
        estadoProgreso(false);
    };
    //Buscamos con el metodo 2 /friend/profile/displayProfile.do?userid=(.*?)\">
    var resultado2 = Documento.match(patron2);
    var concidencia2;
    if (resultado2 != null) {
        while ((concidencia2 = patron2.exec(Documento))) {
            estadoProgreso(true);
            iDsStore.push(concidencia2[1].replace(\"')\", ''));
        };
    } else {
        alert('En esta pagina no se encontraron Perfiles(IDS-2) de Hi5');
        estadoProgreso(false);
    };
    //Arreglamos los arreglos
    iDsStore = eliminarDuplicados(iDsStore);
    document.getElementById('contador').value = 'Encontrados: ' + iDsStore.length + ' IDs(1-2)';
    //Rellenamos la tabla
    var ids = document.getElementById('ids');
    for (i = 0; i < iDsStore.length; i++) {
        //alert(iDsStore[i]);
        var row = document.createElement('listitem');
        row.setAttribute('label', 'UserID:' + iDsStore[i]);
        row.setAttribute('value', iDsStore[i]);
        ids.appendChild(row);
        if (i == iDsStore.length - 1) {
            document.getElementById('progreso').value = 110;
            document.getElementById('proceso1').disabled = 'true';
            estadoProgreso(false);
        };
    };
};

//C-> ENVIAR INVITACIONES
//http://hi5.com/friend/addFriendAjax.do
//timestamp=-283192195017210631&
//js=a10ozYRP2SFa&
//requestSource=FRIENDS_OF_FRIENDS&
//userid=506699145&
//userId=


function c() {
    var Documento = window.opener.content.document.body.innerHTML;
    var jscode_p = /&amp;js=(.*?)\"\>Cerrar/gim;
    var timestamp_p = /logoff.do\?timestamp=(.*?)&amp;js=/gi;
    //Buscamos con el metodo 1 de JScode /friend\/p(.*?)--/
    var resultado1 = Documento.match(jscode_p);
    var concidencia1;
    var jscode;
    if (resultado1 != null) {
        while ((concidencia1 = jscode_p.exec(Documento))) {
            jscode = concidencia1[1];
        };
    } else {
        alert('En esta pagina no se encontraron codigos HI5-JS');
    };
    //Buscamos con el metodo 2 de timestamp /friend\/p(.*?)--/
    var resultado2 = Documento.match(timestamp_p);
    var concidencia2;
    var timestamp;
    if (resultado2 != null) {
        while ((concidencia2 = timestamp_p.exec(Documento))) {
            timestamp = concidencia2[1];
        };
    } else {
        alert('En esta pagina no se encontraron codigos HI5-TS');
    };
    //Info total
    //alert('jscode:'+jscode+' ,timestamp: '+timestamp);
    //Rellenamos la tabla y enviamos la peticion
    var ids = document.getElementById('ids');
    var max = ids.getRowCount();
    for (i = 0; i < max; i++) {
        //ids.removeItemAt(0);
        var user = ids.getItemAtIndex(i).getAttribute('value');
        ids.getItemAtIndex(i).setAttribute('label', ids.getItemAtIndex(i).getAttribute('label') + ' -> Listo!');
        //Envia petecion Ajax
        enviarInvitacion(timestamp, jscode, user);
        document.getElementById('contador').value = 'Enviados: ' + (i + 1) + ' IDs(1-2)';
        if ((i + 1) == max) {
            alert('Felicidades.. Se han enviado ' + max + ' invitaciones!');
            document.getElementById('proceso2').disabled = 'true';
        };
    };
};

```
