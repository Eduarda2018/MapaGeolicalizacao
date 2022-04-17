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

package app.inovatec.mapageolicalizacao;

import androidx.fragment.app.FragmentActivity;

import android.graphics.Color;
import android.os.Bundle;
import android.widget.Toast;

import com.google.android.gms.maps.CameraUpdateFactory;
import com.google.android.gms.maps.GoogleMap;
import com.google.android.gms.maps.OnMapReadyCallback;
import com.google.android.gms.maps.SupportMapFragment;
import com.google.android.gms.maps.model.BitmapDescriptorFactory;
import com.google.android.gms.maps.model.CircleOptions;
import com.google.android.gms.maps.model.LatLng;
import com.google.android.gms.maps.model.MarkerOptions;
import com.google.android.gms.maps.model.PolygonOptions;
import com.google.android.gms.maps.model.PolylineOptions;

public class MapsActivity extends FragmentActivity implements OnMapReadyCallback {

    private GoogleMap mMap;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_maps);

        // Obtain the SupportMapFragment and get notified when the map is ready to be used.
        SupportMapFragment mapFragment = (SupportMapFragment) getSupportFragmentManager()
                .findFragmentById(R.id.map);
        mapFragment.getMapAsync(this);

    }


    /**
     * Manipulates the map once available.
     * This callback is triggered when the map is ready to be used.
     * This is where we can add markers or lines, add listeners or move the camera. In this case,
     * we just add a marker near Sydney, Australia.
     * If Google Play services is not installed on the device, the user will be prompted to install
     * it inside the SupportMapFragment. This method will only be triggered once the user has
     * installed Google Play services and returned to the app.
     */
    @Override
    public void onMapReady(GoogleMap googleMap) {
        mMap = googleMap;

        //Mudar exibição do mapa
        mMap.setMapType(GoogleMap.MAP_TYPE_NORMAL);

        // Add a marker in Sydney and move the camera
        final LatLng chapada = new LatLng(-3.091674031450345, -60.02590636564654);
        //-3.091674031450345, -60.02590636564654



        /*PolygonOptions polygonOptions = new PolygonOptions();
        polygonOptions.add(new LatLng(-23.585406, -46.660405));
        polygonOptions.add(new LatLng(-23.585181, -46.660080));
        polygonOptions.add(new LatLng(-23.586221, -46.659156));
        polygonOptions.add(new LatLng(-23.586447, -46.659472));
        polygonOptions.strokeColor(Color.GREEN);
        polygonOptions.strokeWidth(10);
        polygonOptions.fillColor(Color.argb(128,255, 153, 0));
        mMap.addPolygon( polygonOptions );*/

        //Adicionando evento de clique no mapa
        mMap.setOnMapClickListener(new GoogleMap.OnMapClickListener() {
            @Override
            public void onMapClick(LatLng latLng) {

                Double latitude = latLng.latitude;
                Double longitude = latLng.longitude;

                /*Toast.makeText(MapsActivity.this,
                        "onClick Lat: " + latitude + " long:" + longitude ,
                        Toast.LENGTH_SHORT).show();*/

                PolylineOptions polylineOptions = new PolylineOptions();
                polylineOptions.add(chapada);
                polylineOptions.add(latLng);
                polylineOptions.color(Color.BLUE);
                polylineOptions.width(20);

                mMap.addPolyline(polylineOptions);

                mMap.addMarker(
                        new MarkerOptions()
                                .position(latLng)
                                .title("Policial")
                                .snippet("Descricao")
                                .icon(BitmapDescriptorFactory.fromResource(R.drawable.icone_carro_polici48px))
                );

            }
        });

        mMap.setOnMapLongClickListener(new GoogleMap.OnMapLongClickListener() {
            @Override
            public void onMapLongClick(LatLng latLng) {
                Double latitude = latLng.latitude;
                Double longitude = latLng.longitude;

                Toast.makeText(MapsActivity.this,
                        "onLong Lat: " + latitude + " long:" + longitude,
                        Toast.LENGTH_SHORT).show();

                mMap.addMarker(
                        new MarkerOptions()
                                .position(latLng)
                                .title("Bombeiro")
                                .snippet("Descricao")
                                .icon(BitmapDescriptorFactory.fromResource(R.drawable.icone_carro_bombeiro48px))
                );
            }
        });

        mMap.addMarker(
                new MarkerOptions()
                        .position(chapada)
                        .title("chapada")
                        /*.icon(
                                BitmapDescriptorFactory.defaultMarker(BitmapDescriptorFactory.HUE_ORANGE)
                        )*/
                        .icon(BitmapDescriptorFactory.fromResource(R.drawable.icone_pessoa48px))
        );
        mMap.moveCamera(//2.0 até 21.0
                CameraUpdateFactory.newLatLngZoom(chapada, 18)
        );

    }
}


package app.inovatec.mapageolicalizacao;

import androidx.annotation.NonNull;
import androidx.core.app.ActivityCompat;
import androidx.fragment.app.FragmentActivity;

import android.Manifest;
import android.app.AlertDialog;
import android.content.Context;
import android.content.DialogInterface;
import android.content.pm.PackageManager;
import android.location.Address;
import android.location.Geocoder;
import android.location.Location;
import android.location.LocationListener;
import android.location.LocationManager;
import android.os.Bundle;
import android.util.Log;

import com.google.android.gms.maps.CameraUpdateFactory;
import com.google.android.gms.maps.GoogleMap;
import com.google.android.gms.maps.OnMapReadyCallback;
import com.google.android.gms.maps.SupportMapFragment;
import com.google.android.gms.maps.model.LatLng;
import com.google.android.gms.maps.model.MarkerOptions;

import java.io.IOException;
import java.util.List;
import java.util.Locale;

public class Resgate extends FragmentActivity implements OnMapReadyCallback {

    private GoogleMap mMap;
    private String[] permissoes = new String[]{
            Manifest.permission.ACCESS_FINE_LOCATION
    };
    private LocationManager locationManager;
    private LocationListener locationListener;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_maps);

        //Validar permissões
        Permissoes.validarPermissoes(permissoes, this, 1);

        // Obtain the SupportMapFragment and get notified when the map is ready to be used.
        SupportMapFragment mapFragment = (SupportMapFragment) getSupportFragmentManager()
                .findFragmentById(R.id.map);
        mapFragment.getMapAsync(this);
    }


    /**
     * Manipulates the map once available.
     * This callback is triggered when the map is ready to be used.
     * This is where we can add markers or lines, add listeners or move the camera. In this case,
     * we just add a marker near Sydney, Australia.
     * If Google Play services is not installed on the device, the user will be prompted to install
     * it inside the SupportMapFragment. This method will only be triggered once the user has
     * installed Google Play services and returned to the app.
     */
    @Override
    public void onMapReady(GoogleMap googleMap) {
        mMap = googleMap;

        //Objeto responsável por gerenciar a localização do usuário
        locationManager = (LocationManager) this.getSystemService(Context.LOCATION_SERVICE);

        locationListener = new LocationListener() {
            @Override
            public void onLocationChanged(Location location) {
                Log.d("Localizacao", "onLocationChanged: " + location.toString() );

                Double latitude = location.getLatitude();
                Double longitude = location.getLongitude();

                //-23.593054, -46.663584
                //-23.590679, -46.652288


                /*
                Geocoding -> processo de transformar um endereço
                ou descrição de um local em latitude/longitude
                Reverse Geocoding -> processo de transformar latitude/longitude
                em um endereço
                */
                Geocoder geocoder = new Geocoder(getApplicationContext(), Locale.getDefault() );

                try {

                    //List<Address> listaEndereco = geocoder.getFromLocation(latitude, longitude,1);
                    String stringEndereco = "3532, Av. Constantino Nery, 3470 - Chapada, Manaus - AM, 69010-160";
                    List<Address> listaEndereco = geocoder.getFromLocationName(stringEndereco,1);
                    if( listaEndereco != null && listaEndereco.size() > 0 ){
                        Address endereco = listaEndereco.get(0);

                        /*
                         * onLocationChanged:
                         * Address[
                         *   addressLines=[0:"Av. República do Líbano, 1291 - Parque Ibirapuera, São Paulo - SP, Brazil"],
                         *   feature=1291,
                         *   admin=São Paulo,
                         *   sub-admin=São Paulo,
                         *   locality=São Paulo,
                         *   thoroughfare=Avenida República do Líbano,
                         *   postalCode=null,
                         *   countryCode=BR,
                         *   countryName=Brazil,
                         *   hasLatitude=true,
                         *   latitude=-23.5926719,
                         *   hasLongitude=true,
                         *   longitude=-46.6647561,
                         *   phone=null,
                         *   url=null,
                         *   extras=null]
                         * */

                        Double lat = endereco.getLatitude();
                        Double lon = endereco.getLongitude();

                        mMap.clear();
                        LatLng localUsuario = new LatLng(lat, lon);
                        mMap.addMarker(new MarkerOptions().position(localUsuario).title("Meu local"));
                        mMap.moveCamera(CameraUpdateFactory.newLatLngZoom(localUsuario,18));

                        Log.d("local", "onLocationChanged: " + endereco.getAddressLine(0) );
                    }

                } catch (IOException e) {
                    e.printStackTrace();
                }

            }

            @Override
            public void onStatusChanged(String provider, int status, Bundle extras) {

            }

            @Override
            public void onProviderEnabled(String provider) {

            }

            @Override
            public void onProviderDisabled(String provider) {

            }
        };

        /*
         * 1) Provedor da localização
         * 2) Tempo mínimo entre atualizacões de localização (milesegundos)
         * 3) Distancia mínima entre atualizacões de localização (metros)
         * 4) Location listener (para recebermos as atualizações)
         * */
        if (ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) == PackageManager.PERMISSION_GRANTED) {
            locationManager.requestLocationUpdates(
                    LocationManager.GPS_PROVIDER,
                    0,
                    0,
                    locationListener
            );
        }

    }

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);

        for (int permissaoResultado : grantResults) {

            //permission denied (negada)
            if (permissaoResultado == PackageManager.PERMISSION_DENIED) {
                //Alerta
                alertaValidacaoPermissao();
            } else if (permissaoResultado == PackageManager.PERMISSION_GRANTED) {
                //Recuperar localizacao do usuario

                /*
                 * 1) Provedor da localização
                 * 2) Tempo mínimo entre atualizacões de localização (milesegundos)
                 * 3) Distancia mínima entre atualizacões de localização (metros)
                 * 4) Location listener (para recebermos as atualizações)
                 * */
                if (ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) == PackageManager.PERMISSION_GRANTED) {
                    locationManager.requestLocationUpdates(
                            LocationManager.GPS_PROVIDER,
                            0,
                            0,
                            locationListener
                    );
                }

            }
        }

    }

    private void alertaValidacaoPermissao(){

        AlertDialog.Builder builder = new AlertDialog.Builder(this);
        builder.setTitle("Permissões Negadas");
        builder.setMessage("Para utilizar o app é necessário aceitar as permissões");
        builder.setCancelable(false);
        builder.setPositiveButton("Confirmar", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                finish();
            }
        });

        AlertDialog dialog = builder.create();
        dialog.show();

    }

}
