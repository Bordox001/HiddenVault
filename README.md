// ملف: MainActivity.java package com.hidden.vault;

import android.app.Activity; import android.content.Intent; import android.os.Bundle; import android.view.View; import android.widget.EditText;

public class MainActivity extends Activity { EditText input;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    input = findViewById(R.id.input);
}

public void checkPassword(View v) {
    String entered = input.getText().toString();
    if (entered.equals("2580")) {
        startActivity(new Intent(this, VaultActivity.class));
    } else {
        input.setError("Wrong password");
    }
}

}

// ملف: VaultActivity.java package com.hidden.vault;

import android.app.Activity; import android.os.Bundle; import android.util.Base64; import android.view.View; import android.widget.EditText; import android.widget.Toast;

import java.io.File; import java.io.FileOutputStream; import javax.crypto.Cipher; import javax.crypto.spec.SecretKeySpec;

public class VaultActivity extends Activity { EditText secretText; private final String key = "1234567890123456"; // 16-byte AES key

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_vault);
    secretText = findViewById(R.id.secretText);
}

public void saveSecret(View v) {
    try {
        String data = secretText.getText().toString();
        SecretKeySpec secretKey = new SecretKeySpec(key.getBytes(), "AES");
        Cipher cipher = Cipher.getInstance("AES");
        cipher.init(Cipher.ENCRYPT_MODE, secretKey);
        byte[] encrypted = cipher.doFinal(data.getBytes());
        String output = Base64.encodeToString(encrypted, Base64.DEFAULT);

        File file = new File(getFilesDir(), "hidden.dat");
        FileOutputStream fos = new FileOutputStream(file);
        fos.write(output.getBytes());
        fos.close();

        Toast.makeText(this, "Saved & Encrypted", Toast.LENGTH_SHORT).show();
        secretText.setText("");
    } catch (Exception e) {
        Toast.makeText(this, "Error: " + e.getMessage(), Toast.LENGTH_LONG).show();
    }
}

}

/* activity_main.xml: واجهة بسيطة تشبه الآلة الحاسبة (تطلب كود سري) */

<!-- res/layout/activity_main.xml --><LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="match_parent"
android:orientation="vertical"
android:gravity="center"
android:padding="20dp">

<EditText
    android:id="@+id/input"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Enter PIN"
    android:inputType="numberPassword" />

<Button
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="="
    android:onClick="checkPassword" />

</LinearLayout>/* activity_vault.xml: مكان لتخزين النصوص السرية */

<!-- res/layout/activity_vault.xml --><LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="match_parent"
android:orientation="vertical"
android:padding="20dp">

<EditText
    android:id="@+id/secretText"
    android:layout_width="match_parent"
    android:layout_height="200dp"
    android:hint="اكتب أي شيء سري هنا..."
    android:gravity="top"
    android:inputType="textMultiLine" />

<Button
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="حفظ وتشفير"
    android:onClick="saveSecret" />

</LinearLayout># HiddenVault
