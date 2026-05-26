LAB 17 – MAÎTRISER LES BROADCASTRECEIVER EN ANDROID

Cours : Programmation Mobile – Android avec Java


OBJECTIFS DU LABORATOIRE

Apprendre à créer et utiliser les BroadcastReceiver (composants réagissant aux événements diffusés par le système ou par l’application).

L’application ReceiverDemo réalisera :

- Un Receiver dynamique pour détecter le changement de mode avion (ACTION_AIRPLANE_MODE_CHANGED)
- Un Receiver statique pour BOOT_COMPLETED (démarrage du téléphone)
- L’envoi et la réception d’un Broadcast personnalisé depuis l’Activity
- Comprendre la différence statique vs dynamique, le cycle de vie (onReceive), les permissions et les restrictions Android 14+


PRÉ-REQUIS

- Android Studio
- API minimale 24
- Connaissances Java de base


ÉTAPE 1 – CRÉER LE PROJET (3 minutes)

Android Studio → New Project → Empty Activity
Nom : ReceiverDemo
Langage : Java
Minimum SDK : API 24
Finish


ÉTAPE 2 – CRÉER LE RECEIVER DYNAMIQUE (AIRPLANE MODE) – 8 minutes

Créer une classe AirplaneModeReceiver (package com.example.receiverdemo).

Code :

public class AirplaneModeReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        if (Intent.ACTION_AIRPLANE_MODE_CHANGED.equals(intent.getAction())) {
            boolean isAirplaneOn = intent.getBooleanExtra("state", false);
            String message = isAirplaneOn ? "Mode Avion ACTIVÉ" : "Mode Avion DÉSACTIVÉ";
            Toast.makeText(context, message, Toast.LENGTH_LONG).show();
        }
    }
}


ÉTAPE 3 – CRÉER LE RECEIVER STATIQUE (BOOT_COMPLETED) – 5 minutes

Créer une classe BootReceiver.

Code :

public class BootReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        if (Intent.ACTION_BOOT_COMPLETED.equals(intent.getAction())) {
            Toast.makeText(context, "Téléphone démarré - Receiver statique activé !", Toast.LENGTH_LONG).show();
        }
    }
}


ÉTAPE 4 – DÉCLARER LES RECEIVERS ET PERMISSIONS DANS LE MANIFEST (5 minutes)

Ouvrir AndroidManifest.xml.

Ajouter la permission (avant <application>) :

<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>

Ajouter la déclaration du receiver statique (dans <application>) :

<receiver
    android:name=".BootReceiver"
    android:exported="false">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED" />
    </intent-filter>
</receiver>

Remarque : Le receiver dynamique (AirplaneModeReceiver) n’a pas besoin d’être déclaré dans le Manifest.


ÉTAPE 5 – MODIFIER L’ACTIVITY (12 minutes)

Dans MainActivity.java :

- Déclarer un AirplaneModeReceiver et un booléen isReceiverRegistered.
- Dans onCreate, référencer les boutons et TextView.
- Ajouter un bouton pour activer/désactiver le receiver dynamique.
- Dans toggleAirplaneReceiver(), créer IntentFilter avec ACTION_AIRPLANE_MODE_CHANGED, puis registerReceiver() ou unregisterReceiver().
- Ajouter un bouton pour envoyer un custom broadcast.
- Dans sendCustomBroadcast(), créer un Intent avec une action personnalisée (ex: "com.example.receiverdemo.CUSTOM_EVENT"), ajouter un extra, puis sendBroadcast(intent).
- Dans onDestroy(), désenregistrer le receiver si actif.


ÉTAPE 6 – AJOUTER UN RECEIVER POUR LE CUSTOM BROADCAST (5 minutes)

Créer une classe CustomReceiver (ou réutiliser AirplaneModeReceiver). Dans onReceive, vérifier l’action personnalisée et afficher un Toast avec le message reçu.

Enregistrer ce receiver dynamiquement dans MainActivity (au même moment que le receiver avion ou via un autre bouton).


ÉTAPE 7 – LAYOUT SIMPLE (2 minutes)

activity_main.xml :

- TextView (tvStatus) pour l’état du receiver
- Bouton "Activer/Désactiver Receiver Avion"
- Bouton "Envoyer Custom Broadcast"
- (Optionnel) Bouton pour enregistrer le custom receiver


ÉTAPE 8 – LANCER ET TESTER (5 minutes)

1. Lancer l’app sur un appareil ou émulateur (API 24+).
2. Activer le receiver dynamique via le bouton.
3. Basculer le mode avion (paramètres système). Un Toast doit apparaître.
4. Envoyer le custom broadcast. Le receiver correspondant doit afficher un Toast.
5. Redémarrer l’appareil (ou émulateur). Un Toast doit apparaître au démarrage (receiver statique).


CONCLUSION ET BONNES PRATIQUES

- Dynamique (registerReceiver) : recommandé pour l’écoute temporaire (batterie, UI). À désenregistrer dans onDestroy().
- Statique (déclaration dans Manifest) : réservé aux événements système rares (BOOT_COMPLETED, etc.). exported="false" obligatoire depuis Android 12.
- Ne pas faire d’opérations lourdes dans onReceive (thread principal).
- Pour les broadcasts intra-app sécurisés, préférer LocalBroadcastManager ou les flux LiveData/Flow.

Ce laboratoire couvre les bases essentielles des BroadcastReceiver, utilisés pour réagir aux événements système et internes.
