using UnityEngine;

public class FatehMaidanMasterSystem : MonoBehaviour
{
    [Header("--- 1. लोडिंग स्क्रीन और गेम का परिचय ---")]
    public string gameTitle = "फतेह मैदान (Fateh Maidan)";
    public string engineName = "हिंदुस्तान इंजन (Hindustan Engine)";
    [TextArea]
    public string loadingScreenSlogan = "रण भूमि सजी है, शौर्य का परचम लहराएगा... वही टिकेगा मैदान में, जिसके सीने में जिगर और बाजू में दम होगा!";

    [Header("--- 2. कैरेक्टर 3D मूवमेंट सेटिंग्स (फ्री फायर जैसा मक्खन) ---")]
    private CharacterController controller;
    private Vector3 playerVelocity;
    private bool groundedPlayer;
    public float playerSpeed = 7.0f;
    public float jumpHeight = 2.4f;
    public float gravityValue = -25.0f;

    [Header("--- 3. ऑनर स्कोर (सम्मान अंक) सिस्टम ---")]
    public int playerHonorScore = 100;
    public int minScoreToPlay = 80;

    [Header("--- 4. गन रीकॉइल और शूटिंग मैकेनिक्स ---")]
    public float fireRate = 0.15f;
    public float recoilAmount = 1.5f;
    private float nextFireTime = 0f;

    [Header("--- 5. मल्टी-लैंग्वेज सपोर्ट (English / हिंदी) ---")]
    public enum GameLanguage { English, Hindi }
    public static GameLanguage currentLanguage = GameLanguage.English;

    void Start()
    {
        controller = GetComponent<CharacterController>();
        Debug.Log(gameTitle + " successfully loaded on " + engineName);
        Debug.Log("Loading Slogan: " + loadingScreenSlogan);
    }

    void Update()
    {
        // --- 1. कैरेक्टर मूवमेंट लॉजिक ---
        groundedPlayer = controller.isGrounded;
        if (groundedPlayer && playerVelocity.y < 0)
        {
            playerVelocity.y = 0f;
        }

        float moveX = Input.GetAxis("Horizontal");
        float moveZ = Input.GetAxis("Vertical");
        Vector3 move = new Vector3(moveX, 0, moveZ);
        move = transform.TransformDirection(move);

        controller.Move(move * playerSpeed * Time.deltaTime);

        if (Input.GetButtonDown("Jump") && groundedPlayer)
        {
            playerVelocity.y += Mathf.Sqrt(jumpHeight * -3.0f * gravityValue);
        }

        playerVelocity.y += gravityValue * Time.deltaTime;
        controller.Move(playerVelocity * Time.deltaTime);


        // --- 2. ऑनर स्कोर चेक ---
        CheckHonorSystem();


        // --- 3. शूटिंग और गन रीकॉइल ---
        if (Input.GetButton("Fire1") && Time.time >= nextFireTime)
        {
            nextFireTime = Time.time + fireRate;
            ApplyRecoil();
        }
    }

    void CheckHonorSystem()
    {
        if (playerHonorScore < minScoreToPlay)
        {
            // स्कोर 80 से नीचे होने पर मैचमेकिंग ब्लॉक
            Debug.Log(GetUIText("HonorLowWarning"));
        }
    }

    void ApplyRecoil()
    {
        // गोली चलाते वक्त हथियार का झटका
        transform.Rotate(-recoilAmount, 0f, 0f);
    }

    // भाषा बदलने का फंक्शन (0 = English, 1 = Hindi)
    public void SetLanguage(int langIndex)
    {
        if (langIndex == 0)
        {
            currentLanguage = GameLanguage.English;
            Debug.Log("Language switched to English.");
        }
        else if (langIndex == 1)
        {
            currentLanguage = GameLanguage.Hindi;
            Debug.Log("भाषा बदलकर हिंदी कर दी गई है।");
        }
    }

    // UI और सिस्टम टेक्स्ट का डायनेमिक डेटा
    public string GetUIText(string key)
    {
        if (currentLanguage == GameLanguage.English)
        {
            switch (key)
            {
                case "StartGame": return "START GAME";
                case "Settings": return "SETTINGS";
                case "HonorScore": return "Honor Score";
                case "HonorLowWarning": return "Honor Score is too low! Matchmaking restricted.";
                default: return "";
            }
        }
        else
        {
            switch (key)
            {
                case "StartGame": return "गेम शुरू करें";
                case "Settings": return "सेटिंग्स";
                case "HonorScore": return "सम्मान अंक";
                case "HonorLowWarning": return "ऑनर स्कोर बहुत कम है! मैचमेकिंग प्रतिबंधित है।";
                default: return "";
            }
        }
    }
}
# Mafia-YLC
