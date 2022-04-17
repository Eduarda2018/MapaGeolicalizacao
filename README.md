# MapaGeolicalizacao
package app.inovatec.mapageolicalizacao;

import androidx.appcompat.app.AppCompatActivity;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Button button_social,button_resgate;

        button_social = findViewById(R.id.button_social);
        button_resgate = findViewById(R.id.button_resgate);

        button_social.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Intent map = new Intent(getApplicationContext(),MapsActivity.class);
                startActivity(map);
            }
        });

        button_resgate.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Intent resgate =new Intent(getApplicationContext(),Resgate.class);
                startActivity(resgate);
            }
        });



    }
}
