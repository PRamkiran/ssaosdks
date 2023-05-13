6.AIM: Develop an application that inserts some notifications into Notification area and whenever a notification is inserted, it should show a toast with details of the notification.

XML CODES:

activity_main.xml

<?xml version="1.0" encoding="utf-8"?>
<androidx.appcompat.widget.LinearLayoutCompat xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    tools:context=".MainActivity">

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/csbs"
        android:text="CSBS"
        >
    </Button>
    <Button
        android:layout_width="wrap_content"
        android:id="@+id/aids"
        android:text="AIDS"
        android:layout_height="wrap_content">
    </Button>
</androidx.appcompat.widget.LinearLayoutCompat>

activity_notification.xml

<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".NotifyActivity">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/textnotification"
        ></TextView>

</RelativeLayout>






JAVA CODES:

MainActivity.java

package com.example.csbs06;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.NotificationCompat;
import androidx.core.app.NotificationManagerCompat;
import android.app.Notification;
import android.app.NotificationChannel;
import android.app.NotificationManager;
import android.app.PendingIntent;
import android.content.Intent;
import android.graphics.BitmapFactory;
import android.graphics.Color;
import android.media.Ringtone;
import android.media.RingtoneManager;
import android.net.Uri;
import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Button btncsbs,btnaids;
        btncsbs = findViewById(R.id.csbs);
        btnaids = findViewById(R.id.aids);
        btncsbs.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {

                createNotification(
                        getResources().getString(R.string.channel_csbs),
                        getResources().getString(R.string.csbs_content),
                        getResources().getString(R.string.channel_csbs),
                        NotificationCompat.PRIORITY_HIGH,
                        100
                );


            }
        });
        btnaids.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                createNotification(
                        getResources().getString(R.string.channel_aids),
                        getResources().getString(R.string.aids_content),
                        getResources().getString(R.string.channel_aids),
                        NotificationCompat.PRIORITY_HIGH,
                        100
                );
            }

        });

        }
    private void createNotification(String title, String content,
                                    String channedId, int priorty, int notificationID)

    {
        Intent intent = new Intent(this, NotifyActivity.class);
        intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
        Bundle extras = new Bundle();
        extras.putString(NotifyActivity.notify_title, title);
        extras.putString(NotifyActivity.notify_content, content);
        intent.putExtras(extras);
        intent.setAction(Intent.ACTION_VIEW);
        PendingIntent pendingIntent = PendingIntent.getActivity(getApplicationContext(), notificationID, intent,
                PendingIntent.FLAG_IMMUTABLE);

        NotificationCompat.Builder notificationBuilder =
                new NotificationCompat.Builder(getApplicationContext(), channedId)
                        .setSmallIcon(R.drawable.ic_notifications_active)
                        .setLargeIcon(BitmapFactory.decodeResource(getResources(), R.mipmap.ic_launcher))
                        .setAutoCancel(true)
                        .setLights(Color.BLUE, 500, 500)
                        .setVibrate(new long[]{500, 500, 500})
                        .setPriority(priorty)
                        .setContentTitle(title)
                        .setContentText(content)
                        .setContentIntent(pendingIntent)
                        .setVisibility(NotificationCompat.VISIBILITY_PUBLIC);
        // Since android Oreo notification channel is needed.
        NotificationManagerCompat notificationManager = NotificationManagerCompat.from(MainActivity.this);
        // Since android Oreo notification channel is needed.
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            NotificationChannel channel = new NotificationChannel(channedId,
                    channedId,
                    NotificationManager.IMPORTANCE_HIGH);
            channel.setLockscreenVisibility(NotificationCompat.VISIBILITY_PUBLIC);
            notificationManager.createNotificationChannel(channel);
        }
        Notification notification = notificationBuilder.build();

        notificationManager.notify(notificationID, notification);

        playNotificationSound();

    }

    private void playNotificationSound()
    {

        try {
            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            Ringtone r = RingtoneManager.getRingtone(MainActivity.this, defaultSoundUri);
            r.play();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

NotifyActivity.java

package com.example.csbs06;
import androidx.appcompat.app.AppCompatActivity;
import android.os.Bundle;
import android.widget.TextView;

public class NotifyActivity extends AppCompatActivity {
    public static String notify_title = "notify_title";
    public static String notify_content = "notify_content";

    private TextView textView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_notification);
        textView = findViewById(R.id.textnotification);
        updateUI();
    }
    private void updateUI() {
        String notifyData =
                getIntent().getExtras().get(notify_title) + " \n" +
                       getIntent().getExtras().get(notify_content);
        textView.setText(notifyData);
    }
}
