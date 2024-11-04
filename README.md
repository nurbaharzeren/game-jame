# game-jame

küpleri çarpıştırma kodu:
cubecollision.cs

using UnityEngine;

public class CubeCollision : MonoBehaviour
{
    void OnCollisionEnter(Collision collision)
    {
        // Check if the collided object is tagged as "Cube"
        if (collision.gameObject.CompareTag("Cube"))
        {
            Debug.Log("Collision detected with another cube!");
        }
    }
}


kuplerden birine basında ondeki kup açılıyor kodu
openwall.cs


    using UnityEngine;

public class OpenWall : MonoBehaviour
{
    public GameObject wall;
    public Vector3 openPosition;
    public float speed = 2f;
    
    void OnMouseDown()
    {
        StartCoroutine(OpenWallRoutine());
    }

    System.Collections.IEnumerator OpenWallRoutine()
    {
        while (Vector3.Distance(wall.transform.position, openPosition) > 0.01f)
        {
            wall.transform.position = Vector3.MoveTowards(wall.transform.position, openPosition, speed * Time.deltaTime);
            yield return null;
        }
    }
}

bir nesneyi başka bir nesneyle birleştirme kodu


using UnityEngine;

public class Nesnenin adı : MonoBehaviour
{
    private void OnCollisionEnter(Collision collision)
    {
        // Çarpışılan nesnenin etiketi "Cube" ise
        if (collision.gameObject.CompareTag("Cube"))
        {
            // Çarpışılan nesneyi bu nesnenin pozisyonuna taşıyoruz
            collision.transform.position = transform.position;
            Debug.Log("Küpler birleştirildi!");
        }
    }
}

iki nesne arasına 3bir nesne ekleme kodu

using UnityEngine;

public class CubeMerge : MonoBehaviour
{
    public GameObject mergedCubePrefab; // Birleştirilmiş küp prefab'ı

    private void OnCollisionEnter(Collision collision)
    {
        // Çarpışılan nesnenin etiketi "Cube" ise
        if (collision.gameObject.CompareTag("Cube"))
        {
            // İki küp arasındaki orta noktayı hesaplayalım
            Vector3 middlePoint = (transform.position + collision.transform.position) / 2;

            // Orta noktada yeni bir birleşik küp oluştur
            Instantiate(mergedCubePrefab, middlePoint, Quaternion.identity);

            // İki küpü sahneden kaldır
            Destroy(gameObject);
            Destroy(collision.gameObject);

            Debug.Log("Küpler birleştirildi!");
        }
    }
}

nesneleri birbirinden kuvvetle ayırmak için kullanılan kod

using UnityEngine;

public class CubeSeparate : MonoBehaviour
{
    private Rigidbody rb;

    private void Start()
    {
        rb = GetComponent<Rigidbody>();
    }

    private void OnCollisionEnter(Collision collision)
    {
        // Çarpışılan nesnenin etiketi "Cube" ise
        if (collision.gameObject.CompareTag("Cube"))
        {
            Debug.Log("Çarpışma gerçekleşti! Nesneler ayrılıyor...");

            // Çarpışan diğer nesnenin Rigidbody bileşenini al
            Rigidbody otherRb = collision.gameObject.GetComponent<Rigidbody>();

            // Çarpışma yönünde her iki nesneye zıt kuvvetler uygulayarak ayır
            Vector3 separationDirection = (transform.position - collision.transform.position).normalized;

            rb.AddForce(separationDirection * 5f, ForceMode.Impulse);
            otherRb.AddForce(-separationDirection * 5f, ForceMode.Impulse);
        }
    }
}


Birbirine değince ayrılan nesneler için kod


using UnityEngine;

public class CubeSeparate : MonoBehaviour
{
    private void OnCollisionEnter(Collision collision)
    {
        // Çarpışılan nesnenin etiketi "Cube" ise
        if (collision.gameObject.CompareTag("Cube"))
        {
            Debug.Log("Çarpışma gerçekleşti! Nesneler ayrılıyor...");

            // Çarpışma yönünde her iki nesneyi zıt yönlere taşı
            Vector3 separationDirection = (transform.position - collision.transform.position).normalized;
            float separationDistance = 2f;

            transform.position += separationDirection * separationDistance;
            collision.transform.position -= separationDirection * separationDistance;
        }
    }
}

bir nesneyi başka bir nesnenin içinden geçirne kodu

void Start() {
    // Collider'ı tetikleyici olarak ayarla
    GetComponent<Collider>().isTrigger = true;
}


bir leveldan diğer levela geçme kodu

public class LevelManager : MonoBehaviour
{
    // Geçiş yapmak istediğiniz sahnenin adı
    public string nextLevel;

    // Bu fonksiyon, sahne geçişini gerçekleştirecek
    public void LoadNextLevel()
    {
        SceneManager.LoadScene(nextLevel);
    }
}


bıçak saldırısı için kod

using UnityEngine;
using UnityEngine.UI;
public class BicakSaldiri : MonoBehaviour
{
    
    public Button bicsaldir_buton;
    public Animator bicakel_animator; 
    // saldiri gerekenler
    public GameObject bicaksokmasesi_bicakel;
    public GameObject hasarizi_bicak;
    public Button slot_susturucu_buton, slot_sniper_buton, slot_pompali_buton, slot_keles_buton, bicak_saldiri_buton;
    KarakterHareket player_Hareket; 
    // saldiri mekanigi 
    public float hasar = 10f;
    public float mesafe = 100f;
    public Camera fpsCam;
    public void Start()
    {
        player_Hareket = GameObject.Find("Player").GetComponent<KarakterHareket>();
    }
    public void bic_saldirisi()
    {
        StartCoroutine("bic_attack");
        saldiri();
    }
    IEnumerator bic_attack()
    {
        bicak_saldiri_buton.enabled = false;
        bicakel_animator.SetBool("İdle_bicak", true);
        bicakel_animator.SetBool("Atak_bicak", true);
        player_Hareket.Silah_Tetiklendi = true;
        bicaksokmasesi_bicakel.SetActive(true);
        yield return new WaitForSeconds(0.5f);
        player_Hareket.Silah_Tetiklendi = false;
        bicaksokmasesi_bicakel.SetActive(false);
        bicakel_animator.SetBool("İdle_bicak", false);
        bicakel_animator.SetBool("Atak_bicak", false);
        bicak_saldiri_buton.enabled = true;
    }
    public void saldiri()
    {
        RaycastHit hit;
        if (Physics.Raycast(fpsCam.transform.position, fpsCam.transform.forward, out hit, 4f))
        {
            Debug.Log(hit.transform.name);
            GameObject mermi_izi = Instantiate(hasarizi_bicak, hit.point, Quaternion.LookRotation(hit.normal));
            #region mermi izi siler 
            Destroy(mermi_izi, 1f);
            #endregion
        }
    }
}


bomba başlatıcı için kod


using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using DG.Tweening;
using UnityEngine.UI;
using TMPro;
public class Bomba_baslatici : MonoBehaviour
{
    public Transform baslangic_Noktasi;
    public GameObject bomba;
    public GameObject bombapatlama_Sesi,bombafirlatma_Sesi; 
    public GameObject bicak_el, pompali_el, sniper_el, susturucu_el;
    public TextMeshProUGUI bomba_sayi_text;
    public float bomba_sayisi = 4f;
 
    public Button bombabt; 
    float range = 15f;
    public CameraShake camera_shake;
    void Start()
    {
        bomba_sayi_text.text = "" + bomba_sayisi;
        bombapatlama_Sesi.SetActive(false);
        bombafirlatma_Sesi.SetActive(false);
    }
    public void Update()
    {
        bomba_sayi_text.text = "" + bomba_sayisi;
    }
    public void Bomba_Baslatici()
    {
        if(bomba_sayisi > 0)
        {
            GameObject bomba_tur = Instantiate(bomba, baslangic_Noktasi.position, baslangic_Noktasi.rotation);
            bomba_tur.GetComponent<Rigidbody>().AddForce(baslangic_Noktasi.forward * range, ForceMode.Impulse);
            StartCoroutine("bombpatlamasesi");
            bomba_sayisi--;
            if (bicak_el.activeSelf != false || pompali_el.activeSelf != false || sniper_el.activeSelf != false || susturucu_el.activeSelf != false)
            {
                StartCoroutine("bomba_cooldown");
                StartCoroutine("silah_indirme");
                StartCoroutine("camera_shakeolay");
            }
        }
    }
    IEnumerator silah_indirme()
    {
      
        if(bicak_el.activeSelf != false)
        bicak_el.transform.DOMoveY(bicak_el.transform.position.y - 0.05f, 0.6f);
        if(sniper_el.activeSelf != false)
        sniper_el.transform.DOMoveY(sniper_el.transform.position.y - 0.10f, 0.6f);
        if(pompali_el.activeSelf != false)
        pompali_el.transform.DOMoveY(pompali_el.transform.position.y - 0.10f, 0.6f);
        if(susturucu_el.activeSelf != false)
        susturucu_el.transform.DOMoveY(susturucu_el.transform.position.y - 0.10f, 0.6f);
        yield return new WaitForSeconds(0.6f);
        
        if (bicak_el.activeSelf != false)
            bicak_el.transform.DOMoveY(bicak_el.transform.position.y + 0.05f, 0.6f);
        
        if (sniper_el.activeSelf != false)
            sniper_el.transform.DOMoveY(sniper_el.transform.position.y + 0.10f, 0.6f);
        if (pompali_el.activeSelf != false)
            pompali_el.transform.DOMoveY(pompali_el.transform.position.y + 0.10f, 0.6f);
        if (susturucu_el.activeSelf != false)
            susturucu_el.transform.DOMoveY(susturucu_el.transform.position.y + 0.10f, 0.6f);
    }
    IEnumerator bomba_cooldown()
    {
        bombabt.enabled = false;
        yield return new WaitForSeconds(3f);
        bombabt.enabled = true;
    }
    IEnumerator bombpatlamasesi()
    {
        bombafirlatma_Sesi.SetActive(true);
        bombapatlama_Sesi.SetActive(true);
        yield return new WaitForSeconds(3f);
        
        bombafirlatma_Sesi.SetActive(false);
        bombapatlama_Sesi.SetActive(false);
    }
    IEnumerator camera_shakeolay()
    {
        yield return new WaitForSeconds(2f);
        StartCoroutine(camera_shake.Shake(.15f, .2f));
    }
    
}

hedef belirleme kod

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.AI;
using DG.Tweening;
using UnityEngine.Audio; 
public class DefenderAi : MonoBehaviour
{
    public Transform[] varis_noktalari; // varýþ noktalarýn transformlarý 
    public Transform Dost; 
    int anlikVarisNoktaÝndeksi; // anlik varýþ noktasýnýn indexi 
    NavMeshAgent nav; // dusmanýn navmeshagent'ý
    float anlik_BeklemeSuresi; // varis noktalarina ulaþýldýgýnda beklemek için gerekilen 2 parametre
    float max_BeklemeSuresi;
    float mesafe;
    public bool Gezebilir;
    Level1SpawnManager spawn_manager;
    public GameObject Kosma_Sound;
    AudioSource kosmasoun_component;
    public int can = 100; 
    Animator ai_anim; 
    void Start()
    {
        spawn_manager = GameObject.Find("SpawnManager").GetComponent<Level1SpawnManager>();
        kosmasoun_component = Kosma_Sound.GetComponent<AudioSource>();
        Kosma_Sound.SetActive(false);
        Gezebilir = true;
        Dost = GameObject.FindGameObjectWithTag("oyuncu").transform;
        nav = GetComponent<NavMeshAgent>();
        ai_anim = GetComponent<Animator>();
        anlikVarisNoktaÝndeksi = 0;
        anlik_BeklemeSuresi = 0;
        max_BeklemeSuresi = 0;
        
        ai_anim.SetBool("durdu", true);
        Invoke("YeniVarisNoktasi", 10f);
    }
    
    void Update()
    {
        mesafe = Vector3.Distance(gameObject.transform.position, Dost.transform.position);
        #region Gezme ile alakalý 
        if (Gezebilir == true)
        {
            if (nav.remainingDistance < 0.5f)
            {
                Kosma_Sound.SetActive(false);
                if (max_BeklemeSuresi == 0)
                {
                    max_BeklemeSuresi = Random.Range(0f, 5f);
                }
                if (anlik_BeklemeSuresi >= max_BeklemeSuresi)
                {
                    max_BeklemeSuresi = 0;
                    anlik_BeklemeSuresi = 0;
                    YeniVarisNoktasi();
                }
                else
                {
                    ai_anim.SetBool("durdu", true);
                    anlik_BeklemeSuresi += Time.deltaTime;
                }
            }
            if (nav.remainingDistance > 0.5f)
            {
                Kosma_Sound.SetActive(true);
                ai_anim.SetBool("durdu", false);
            }
        }
        #endregion
        #region dost objesýný taninladigi zaman olacaklar
        if (mesafe < 17f && spawn_manager.terorist_Secildi == true)
        {
            Gezebilir = false;
            nav.SetDestination(Dost.transform.position);
            if(mesafe < 10f)
            {
                
                nav.isStopped = true;
                ai_anim.SetBool("durdu", true);
                Kosma_Sound.SetActive(false);
                transform.LookAt(new Vector3(Dost.transform.position.x,this.transform.position.y,Dost.transform.position.z)); 
            }
        }
        else 
        {
            Gezebilir = true;
            nav.isStopped = false;
            Kosma_Sound.SetActive(true);
        }
        #endregion
        #region Kosma sesini yaklasmaya gore ayarlama
        if (mesafe > 25f)
        {
            kosmasoun_component.volume = 0.20f;
        }
        else if (mesafe < 25f && mesafe > 17f)
        {
            kosmasoun_component.volume = 0.40f;
        }
        else if (mesafe < 17f)
        {
            kosmasoun_component.volume = 1f;
        }
        #endregion
        if(can <= 0)
        {
            ai_anim.SetBool("dead", true);
            nav.enabled = false;
            kosmasoun_component.enabled = false; 
        }
        Debug.Log(mesafe);
    }
     
    void YeniVarisNoktasi() // bu kod blogu yený varýs noktasýna ilerlememizi saglar! 
    {
        if(varis_noktalari.Length != 0 && Gezebilir == true)
        {
            anlikVarisNoktaÝndeksi = Random.Range(0,59);
            ai_anim.SetBool("durdu", false);
            nav.SetDestination(varis_noktalari[anlikVarisNoktaÝndeksi].position);
        }
    }
    public void CanAzalt(int hasar)
    {
        can -= hasar; 
    }
}

el bombası atma kodu

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
public class GrenadeSistemi : MonoBehaviour
{
    public GameObject patlama_efekti;
    public float gecikme_suresi = 2f;
    public float patlama_gucu = 10f;
    public float genislik = 20f;
   
    void Start()
    {
        Invoke("Patlama", gecikme_suresi);
    }
    public void Patlama()
    {
        Collider[] colliders = Physics.OverlapSphere(transform.position, genislik);
        foreach(Collider near in colliders)
        {
            Rigidbody rig = near.GetComponent<Rigidbody>();
            if(rig != null)
            {
                rig.AddExplosionForce(patlama_gucu, transform.position, genislik, 1f, ForceMode.Impulse);
            }
        }
       
        Instantiate(patlama_efekti, transform.position, transform.rotation);
        Destroy(gameObject);
    }
   
   
}

kamera shake(sallama) kod

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
public class Kamera_shake : MonoBehaviour
{
   public IEnumerator Shake(float duration, float magnitude)
    {
        Vector3 orginalPos = transform.localPosition;
        float elapsed = 0.0f;
        while (elapsed < duration)
        {
            float x = Random.Range(-1f, 1f) * magnitude;
            float y = Random.Range(-1f, 1f) * magnitude;
            transform.localPosition = new Vector3(x, y, orginalPos.z);
            elapsed += Time.deltaTime;
            yield return null;
            
        }
        transform.localPosition = orginalPos; 
    }
}

krakter hareketleri

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
public class KarakterHareket : MonoBehaviour
{
    public CharacterController controller;
    public float hiz = 12f;
    public float yercekimi = -9.81f;
    public float ziplama_yuksekligi = 3f;
    Vector3 velocity;
    public Transform zemin_algilayici; // zemini algýlasýn diye playerin en altýna koydugumuz bir game object
    public float zemineMesafe = 0.4f; // zemýný algýlayan obje ýle karakter arasýndaki mesafe ölçüsü 
    public LayerMask zemin_Mask; // layer mask tanýmladýk. Bu sayede layeri zemin olan objelere eriþebiliyoruz
    bool zemine_Degdi; // zemine deðip deðmediðini kontrol ediyoruz. Bu sayede zýplama fonksiyonunu çalýþtýrýyoruz
    public FixedJoystick joystick_hareket; // karakter hareketi için gerekli olan joystick
    public Image joystickhareket_handle; // karakter kostugunu belirtsin diye joystiðin handle' ý 
    // stamina 
    [Range(0,30)]
    public float stamina;
    public float maX_Stamina;
    public Slider stamina_bar;
    public float dValue;
    public bool Silah_Tetiklendi; // bu boolean yururken ates etmeye bastýðýmýzda anýmasyonlarý kontrol eder 
    // sesler 
    public GameObject yurumesesi_player;
    public GameObject ziplamasesi_player;
    public GameObject kosmasesi_player;
    // tabanca el ile alakalý componentler burda
    public Animator tabancael_anim,pompaliel_anim,sniperel_anim,bicakel_anim;
    
    
   
    void Start()
    {
        maX_Stamina = stamina;
        maX_Stamina = 30f; 
        stamina_bar.maxValue = maX_Stamina;
        stamina_bar.minValue = 0f;
        Silah_Tetiklendi = false; 
       
    }
    // Update is called once per frame
    void Update()
    {
        maX_Stamina = 30f;
        joystickhareket_handle.color = Color.white;
        zemine_Degdi = Physics.CheckSphere(zemin_algilayici.position, zemineMesafe, zemin_Mask); 
        if(zemine_Degdi && velocity.y < 0)
        {
            velocity.y = -2f; 
        }
        float x = joystick_hareket.Horizontal;
        float z = joystick_hareket.Vertical;
        Vector3 hareket = transform.right * x + transform.forward * z;
        controller.Move(hareket * hiz * Time.deltaTime);
        velocity.y += yercekimi * Time.deltaTime;
        controller.Move(velocity * Time.deltaTime);
    
        if(Input.GetButtonDown("Jump") && zemine_Degdi)
        {
            velocity.y = Mathf.Sqrt(ziplama_yuksekligi * -2f * yercekimi);
        }
        #region kosma
        
        if(joystick_hareket.Horizontal >= 0.8f || joystick_hareket.Horizontal <= -0.8f || joystick_hareket.Vertical <= -0.8f || joystick_hareket.Vertical >= 0.8f)
        {
            joystickhareket_handle.color = Color.red;
            kosmasesi_player.SetActive(true);
            yurumesesi_player.SetActive(false);
            Stamina_Azaltma();
        }
        else if (stamina != maX_Stamina)
        {
            kosmasesi_player.SetActive(false);
            hiz = 5f; 
            Stamina_Arttýrma();
        }
        if(joystick_hareket.Horizontal != 0 && Silah_Tetiklendi != true || joystick_hareket.Vertical != 0 && Silah_Tetiklendi != true)
        {
            yurumesesi_player.SetActive(true);
            
                #region silah yurume animleri
                tabancael_anim.SetBool("idle", true);
                tabancael_anim.SetBool("yurume_susturucu", true);
                pompaliel_anim.SetBool("Ýdle_pompali", true);
                pompaliel_anim.SetBool("Hareket_pompali", true);
                sniperel_anim.SetBool("Ýdle_sniper", true);
                sniperel_anim.SetBool("Hareket_sniper", true);
                bicakel_anim.SetBool("Ýdle_bicak", true);
                bicakel_anim.SetBool("Hareket_bicak", true);
                #endregion
           
        }
        else if (Silah_Tetiklendi == true)
        {
            //tabancael_anim.SetBool("yurume_susturucu", false);
            //pompaliel_anim.SetBool("Hareket_pompali", false);
            //sniperel_anim.SetBool("Hareket_sniper", false);
            //bicakel_anim.SetBool("Hareket_bicak", false);
            
            yurumesesi_player.SetActive(false);
            #region silah yurume animleri
            tabancael_anim.SetBool("idle", true);
            tabancael_anim.SetBool("yurume_susturucu", false);
            pompaliel_anim.SetBool("Ýdle_pompali", true);
            pompaliel_anim.SetBool("Hareket_pompali", false);
            sniperel_anim.SetBool("Ýdle_sniper", true);
            sniperel_anim.SetBool("Hareket_sniper", false);
            bicakel_anim.SetBool("Ýdle_bicak", true);
            bicakel_anim.SetBool("Hareket_bicak", false);
            #endregion
        }
        else
        {
            tabancael_anim.SetBool("yurume_susturucu", false);
            pompaliel_anim.SetBool("Hareket_pompali", false);
            sniperel_anim.SetBool("Hareket_sniper", false);
            bicakel_anim.SetBool("Hareket_bicak", false);
            yurumesesi_player.SetActive(false);
        }
        stamina_bar.value = stamina;
       
        #endregion
    }
   
    public void Jump()
    {
        if (zemine_Degdi)
        {
            velocity.y = Mathf.Sqrt(ziplama_yuksekligi * -2f * yercekimi);
            StartCoroutine("ziplamasound");
        }
    }
   public void Stamina_Azaltma()
    {
        if(stamina != 0)
        {
            hiz = 8f;
            stamina -= dValue * Time.deltaTime*0.5f;
        }
        else
        {
            stamina = 0f;
            kosmasesi_player.SetActive(false);
        }
    }
    public void Stamina_Arttýrma()
    {
        stamina += dValue * Time.deltaTime*0.5f;
    }
    public void FixedUpdate()
    {
        if (stamina >= 30f)
        {
            stamina = 30f;
        }
        else if (stamina <= 0)
        {
            stamina = 0f;
            joystickhareket_handle.color = Color.magenta;
            hiz = 5f;
        }
    }
    IEnumerator ziplamasound()
    {
        ziplamasesi_player.SetActive(true);
        yield return new WaitForSeconds(0.3f);
        ziplamasesi_player.SetActive(false);
    }
}

1. seviye üreme yöneticisi

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
public class Level1SpawnManager : MonoBehaviour
{
    public GameObject t_spawn_point, ct_spawn_point;
    public GameObject map_cam_genel; 
    public GameObject canvas_secim, canvas_genel;
    public GameObject karakter;
    public GameObject[] defender_Ai;
    public GameObject baslangic_music;
    public bool terorist_Secildi; 
    Vector3 t_Spawn_point_vec,ct_Spawn_point_vec;
    public Timer TÝMER; 
    
    public void Awake()
    {
        defender_Ai[0].SetActive(false);
        defender_Ai[1].SetActive(false);
        defender_Ai[2].SetActive(false);
        defender_Ai[3].SetActive(false);
        baslangic_music.SetActive(true);
        canvas_secim.SetActive(true);
        canvas_genel.SetActive(false);
        map_cam_genel.SetActive(true);
        karakter.SetActive(false);
        t_Spawn_point_vec = t_spawn_point.transform.position;
        ct_Spawn_point_vec = ct_spawn_point.transform.position; 
    }
    public void defender_secim()
    {
        map_cam_genel.SetActive(false);
        canvas_secim.SetActive(false);
        canvas_genel.SetActive(true);
        Time.timeScale = 1;
        karakter.transform.position = ct_Spawn_point_vec;
        karakter.transform.rotation = Quaternion.Euler(0f, 180f, 0f);
        karakter.SetActive(true);
        baslangic_music.SetActive(false);
        defender_Ai[0].SetActive(true);
        defender_Ai[1].SetActive(true);
        defender_Ai[2].SetActive(true);
        defender_Ai[3].SetActive(false);
        terorist_Secildi = false;
        TÝMER.zaman_kontrol = true;
    }
    public void attacker_secim()
    {
        map_cam_genel.SetActive(false);
        canvas_secim.SetActive(false);
        canvas_genel.SetActive(true);
        Time.timeScale = 1;
        karakter.transform.position = t_Spawn_point_vec;
        karakter.SetActive(true);
        baslangic_music.SetActive(false);
        defender_Ai[0].SetActive(true);
        defender_Ai[1].SetActive(true);
        defender_Ai[2].SetActive(true);
        defender_Ai[3].SetActive(true);
        terorist_Secildi = true;
        TÝMER.zaman_kontrol = true;
    }
    void Update()
    {
        
    }
}

anamenu kod

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;
using UnityEngine.Audio; 
public class MainMenu : MonoBehaviour
{
    public GameObject Harita_secim_panel;
    public GameObject Cikis_Uyari_Panel,Market_Panel,Ayarlar_Panel,DailyReward_Panel,Congrulations_Panel;
    public AudioSource MainMenu_Sound; 
    
    public void Harita_Secim_Ac()
    {
        Harita_secim_panel.SetActive(true);
    }
    public void Harita_Secim_Kapat()
    {
        Harita_secim_panel.SetActive(false);
    }
    public void CikisPanelAc()
    {
        if(Cikis_Uyari_Panel.activeSelf != true)
        {
            Cikis_Uyari_Panel.SetActive(true);
        }
        
    }
    public void CikisPanelKapat()
    {
        if (Cikis_Uyari_Panel.activeSelf == true)
        {
            Cikis_Uyari_Panel.SetActive(false);
        }
    }
    public void MarketPanelAc()
    {
        if(Market_Panel.activeSelf != true)
        {
            Market_Panel.SetActive(true);
        }
    }
    public void MarketPanelKapat()
    {
        if (Market_Panel.activeSelf == true)
        {
            Market_Panel.SetActive(false);
        }
    }
    public void AyarlarPanelAc()
    {
        if(Ayarlar_Panel.activeSelf != true)
        {
            Ayarlar_Panel.SetActive(true);
        }
    }
    public void AyarlarPanelKapat()
    {
        if (Ayarlar_Panel.activeSelf == true)
        {
            Ayarlar_Panel.SetActive(false);
        }
    }
    public void OdulPanelAc()
    {
        if (DailyReward_Panel.activeSelf != true)
        {
            DailyReward_Panel.SetActive(true);
        }
    }
    public void OdulPanelKapat()
    {
        if (DailyReward_Panel.activeSelf == true)
        {
            DailyReward_Panel.SetActive(false);
        }
    }
   
    public void CongrulationsPanelKapat()
    {
        if (DailyReward_Panel.activeSelf == true)
        {
            Congrulations_Panel.SetActive(false);
            MainMenu_Sound.UnPause();
        }
    }
    public void OyundanCik()
    {
        Application.Quit();
    }
}

marketmaneger.cs

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using TMPro; 
public class MarketManager : MonoBehaviour
{
    public float para = 400f;
    public string[] silahlar;
    public float[] fiyatlar;
    public TextMeshProUGUI para_text,para_text2; 
    public GameObject sniper_el, pompali_el, tabanca_el;
    public GameObject sniper_slot_bt, pompali_slot_bt, tabanca_slot_bt;
    public Bomba_baslatici bomb_sistem;
    void Start()
    {
        sniper_el.SetActive(false);
        pompali_el.SetActive(false);
        tabanca_el.SetActive(false);
        // --
        sniper_slot_bt.SetActive(false);
        pompali_slot_bt.SetActive(false);
        tabanca_slot_bt.SetActive(false);
        para_text.text = "PARA : " + para + "" + "£";
        para_text2.text = "PARA : " + para + "" + "£";
    }
    void Update()
    {
        
    }
    public void satin_Al(string isim)
    {
        for (int i = 0; i < silahlar.Length; i++)
        {
            if(silahlar[i] == isim)
            {
              
                if(silahlar[i] == "Pompali" && para >= fiyatlar[i] && pompali_slot_bt.activeSelf == false )
                {
                    para -= fiyatlar[i];
                    pompali_slot_bt.SetActive(true);
                    para_text.text = "PARA : " + para + "" + "£";
                    para_text2.text = "PARA : " + para + "" + "£";
                }
                if (silahlar[i] == "Sniper" && para >= fiyatlar[i] && sniper_slot_bt.activeSelf == false)
                {
                    para -= fiyatlar[i];
                    sniper_slot_bt.SetActive(true);
                    para_text.text = "PARA : " + para + "" + "£";
                    para_text2.text = "PARA : " + para + "" + "£";
                }
                if (silahlar[i] == "Tabanca" && para >= fiyatlar[i] && tabanca_slot_bt.activeSelf == false)
                {
                    para -= fiyatlar[i];
                    tabanca_slot_bt.SetActive(true);
                    para_text.text = "PARA : " + para + "" + "£";
                    para_text2.text = "PARA : " + para + "" + "£";
                }
                if (silahlar[i] == "Bomba" && para >= fiyatlar[i] && bomb_sistem.bomba_sayisi < 4)
                {
                    para -= fiyatlar[i];
                    bomb_sistem.bomba_sayisi++;
                    para_text.text = "PARA : " + para + "" + "£";
                    para_text2.text = "PARA : " + para + "" + "£";
                }
            }
        }
    }
}

medkit.cs (tibbi)

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using TMPro;
public class MedKitSistemi : MonoBehaviour
{
    public Image can_bar;
    public TextMeshProUGUI medkit_sayi_text;
    public int medkit_sayi = 3; 
    public void Start()
    {
        medkit_sayi_text.text = "" + medkit_sayi;
    }
    public void CanYenileme()
    {
        if(can_bar.fillAmount < 1 && medkit_sayi > 0 )
        {
            can_bar.fillAmount = 1f;
            medkit_sayi--;
        }
    }
    public void Update()
    {
        medkit_sayi_text.text = "" + medkit_sayi;
    }
}

menumarketsistem.cs

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using TMPro; 
public class MenuMarketSistem : MonoBehaviour
{
    public Text CoinNumberText, SpecialCardNumberText, CoinNumberMarketText, SpecialCardNumberMarketText;
    public int CoinNumber, SpecialCardNumber; 
    void Start()
    {
        // coinlerle alakalý 
        CoinNumber = PlayerPrefs.GetInt("Coin");
        PlayerPrefs.GetInt("Coin");
        CoinNumberText.text = PlayerPrefs.GetInt("Coin").ToString();
        //special card ile alakalý
        SpecialCardNumber = PlayerPrefs.GetInt("Sc");
        PlayerPrefs.GetInt("Sc");
        SpecialCardNumberText.text = PlayerPrefs.GetInt("Sc").ToString();
        //Marketteki coin ve kard ile alakalý
        CoinNumberMarketText.text = CoinNumber.ToString();
        SpecialCardNumberMarketText.text = PlayerPrefs.GetInt("Sc").ToString();
    }
    // Update is called once per frame
    void Update()
    {
        CoinNumberText.text = CoinNumber.ToString();
        SpecialCardNumberText.text = PlayerPrefs.GetInt("Sc").ToString();
        CoinNumberMarketText.text = CoinNumber.ToString();
        SpecialCardNumberMarketText.text = PlayerPrefs.GetInt("Sc").ToString();
    }
    public void CoinArttir(int artanCoinSayisi)
    {
        CoinNumber += artanCoinSayisi;
        PlayerPrefs.SetInt("Coin", CoinNumber);
    }
    public void SpecialCardArttir(int artanScSayisi)
    {
        SpecialCardNumber += artanScSayisi;
        PlayerPrefs.SetInt("Sc", SpecialCardNumber);
    }
    public void CoinAzalt(int azaltanCoinSayisi)
    {
        CoinNumber += azaltanCoinSayisi;
        PlayerPrefs.SetInt("Coin", CoinNumber);
    }
    public void SpecialCardAzalt(int azaltanScSayisi)
    {
        SpecialCardNumber += azaltanScSayisi;
        PlayerPrefs.SetInt("Sc", SpecialCardNumber);
    }
}

mouselook.cs 

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
public class MouseLook : MonoBehaviour
{
    public float MouseHassasiyet = 100f; // kameramýza hassasiyet tanýmladýk
    public FloatingJoystick joystick_look; // kameramýzýn bakýþ hareketi için gerekli joystick
    public Transform karakter_Vucudu; // kameramýzla beraber karakterin vüdunun dönmesini saðladýk
    float xRotation = 0f; // 
    // Start is called before the first frame update
    void Start()
    {
        //Cursor.lockState = CursorLockMode.Locked; // mouse görünürlüðünü kilitledik yani durdurduk 
    }
    // Update is called once per frame
    void Update()
    {
        
        float mouseX = joystick_look.Horizontal * MouseHassasiyet * Time.deltaTime; // farenin x kordinatýndaki verileri tuttuk
        float mouseY = joystick_look.Vertical * MouseHassasiyet * Time.deltaTime; // farenin y kordinatýndaki verileri tuttuk 
        xRotation -= mouseY; // xRotation deðerimizi mouseY den çýkarýlmasýna eþitledik
        xRotation = Mathf.Clamp(xRotation, -90f, 90f); // yukarý ve aþaðý bakarken xRotation deðerimizi -90 ile 90 deðerleri arasýnda kelepçeledim yani sýnýrlandýrdým 
        transform.localRotation = Quaternion.Euler(xRotation, 0f, 0f); // transformumuzun local rotasyonunu ayarladým
        karakter_Vucudu.Rotate(Vector3.up * mouseX); // karakterin vücudunun dönmesini saðladým 
    }
}

pompalısaldırı.cs

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using TMPro;
public class PompaliSaldiri : MonoBehaviour
{
    public GameObject pompali_el; // susturucu el olan objenin kendisi
    public Animator pompali_anim; // susturucu elin animatoru
    // mermi sistemi 
    public float mermi_Sayisi = 8f; // baslangýctaki mermi sayýmýzý belirledik 
    public Button atesetme_button; // bu butonu tanýmlama amacým: eger silah reload durumuna girerse mermi saysýnýn azlmasýna engel olmaktýr
    public Button Reload_button;
    public Button slot_susturucu_buton, slot_sniper_buton, slot_pompali_buton, slot_keles_buton, bicak_saldiri_buton;
    public TextMeshProUGUI mermisayi_text; // bu bize ekranda kaç mermi kaldýðýmýzý göstericek 
    // ates sistemi gerekilenler
    public GameObject atessesi_pompali;
    public GameObject reloadesei_pompali;
    public GameObject muzzleflash_pompali;
    public GameObject mermifirlama_pompali;
    public GameObject mermiizi_pompali; // mermi efecti calýstýrýr
    // ates sistemi mekanigi
    public float mesafe = 100f;
    public Camera fpsCam;
    // recoil sistem
    public float minX, maxX;
    public float minY, maxY;
    public Transform camera_fps;
    public Vector3 rot;
    // karakter hareketine erisim saglamak icin 
    KarakterHareket player_Hareket;
    // dusmana hit ile alakali icerikler
    Animator dusman_anim;
    Level1SpawnManager spawn_manager;
    public GameObject hit_sound; // hasar verme sesi 
    public Image crosshair_image;
    DefenderAi defender_ai; 
    public void Pompaliel_Ates()
    {
        AtesMekanigi();
        recoil();
        StartCoroutine("muzzle_flash_control");
        StartCoroutine("atesetme"); // bu fonksiyon bize ates etme butonuna týkladýgýmýzda ates etmeyý saglar
    }
    public void Pompali_Reload()
    {
        if (mermi_Sayisi < 12)
        {
            StartCoroutine("reload"); // bu fonksiyon bize silahýn reload durumuna girmesini saglar 
            
        }
    }
    public void Start()
    {
        PlayerPrefs.GetFloat("pompali_mermi");
        player_Hareket = GameObject.Find("Player").GetComponent<KarakterHareket>();
        spawn_manager = GameObject.Find("SpawnManager").GetComponent<Level1SpawnManager>();
    }
    public void Update()
    {
        pompali_el.transform.localPosition = new Vector3(50f, -1.411f, 0.6870003f);
        PlayerPrefs.SetFloat("pompali_mermi", mermi_Sayisi); // tabanca mermisini kayýt ettik
        mermisayi_text.text = PlayerPrefs.GetFloat("pompali_mermi") + "" + "/" + "" + "999"; // ana sayfada kalan mermi sayýmýzý gösterdik 
        #region oto_reload 
        if (mermi_Sayisi <= 0) // bu blok bize mermi 0 dan düþük oldugunda oto reload etmesini saðlar
        {
            StartCoroutine("reload");
        }
        #endregion
        #region recoil efekt hesaplamalari // recoil efektleri
        rot = camera_fps.transform.localRotation.eulerAngles; // recoil efekt için kameranýn rotasyonunu kaydettik
        if (rot.x != 0 || rot.y != 0)
        {
            camera_fps.transform.localRotation = Quaternion.Slerp(camera_fps.transform.localRotation, Quaternion.Euler(0, 0, 0), Time.deltaTime * 3); // bu kod recoil olduktan sonra kameranýn yavas tavas asagý ýnmesýný saglar
        }
        #endregion
    }
    IEnumerator atesetme() // ates etme olaylarýný bir numerator ýle döndürdüm
    {
        
        pompali_anim.SetBool("Ýdle_pompali", true);
        pompali_anim.SetBool("AtesEtme_pompali", true);
        atesetme_button.enabled = false; // ust uste týklayýp anýmasyon cakýsmasýný engellemek istedim
        bicak_saldiri_buton.enabled = false;
        player_Hareket.Silah_Tetiklendi = true;
        mermifirlama_pompali.SetActive(true);
        atessesi_pompali.SetActive(true);
        mermi_Sayisi--;
        yield return new WaitForSeconds(0.9f);
        player_Hareket.Silah_Tetiklendi = false; 
        mermifirlama_pompali.SetActive(false);
        atessesi_pompali.SetActive(false);
        atesetme_button.enabled = true; // butonu tekrardan aktifleþtirerek sýkabilmesini saðladým 
        pompali_anim.SetBool("AtesEtme_pompali", false);
        bicak_saldiri_buton.enabled = true;
    }
    IEnumerator reload() // reload olaylarýný bir numerator ile döndürdüm
    {
        pompali_el.transform.rotation = Quaternion.Euler(10f, 10f, 10f);
        pompali_anim.SetBool("Ýdle_pompali", true);
        pompali_anim.SetBool("AtesEtme_pompali", false);
        pompali_anim.SetBool("Reload_pompali", true);
        atesetme_button.enabled = false;
        Reload_button.enabled = false;
        mermi_Sayisi = 0f;
        reloadesei_pompali.SetActive(true);
        slot_keles_buton.enabled = false;
        slot_pompali_buton.enabled = false;
        slot_sniper_buton.enabled = false;
        slot_susturucu_buton.enabled = false;
        bicak_saldiri_buton.enabled = false;
        yield return new WaitForSeconds(2f);
        reloadesei_pompali.SetActive(false);
        Reload_button.enabled = true;
        mermi_Sayisi = 12f;
        atesetme_button.enabled = true;
        pompali_anim.SetBool("Reload_pompali", false);
        slot_keles_buton.enabled = true;
        slot_pompali_buton.enabled = true;
        slot_sniper_buton.enabled = true;
        slot_susturucu_buton.enabled = true;
        bicak_saldiri_buton.enabled = true;
    }
    IEnumerator muzzle_flash_control()
    {
        muzzleflash_pompali.SetActive(true);
        yield return new WaitForSeconds(0.1f);
        muzzleflash_pompali.SetActive(false);
    }
    void AtesMekanigi()
    {
        RaycastHit hit;
        if (Physics.Raycast(fpsCam.transform.position, fpsCam.transform.forward, out hit, 50f))
        {
            dusman_anim = hit.transform.gameObject.GetComponent<Animator>();
            defender_ai = hit.transform.gameObject.GetComponent < DefenderAi >();
            Debug.Log(hit.transform.name);
            GameObject mermi_izi = Instantiate(mermiizi_pompali, hit.point, Quaternion.LookRotation(hit.normal));
            #region mermi izi siler 
            Destroy(mermi_izi, 2f);
            #endregion
            // eger terorist olursak ve ct de olmasini istediðimiz seyler varsa burasini kullanýcaz
            if(hit.transform.gameObject.tag == "ct" && spawn_manager.terorist_Secildi == true)
            {
                StartCoroutine("dusman_hit_aldi_ct");
                defender_ai.CanAzalt(30);
            }
        }
    } 
    public void recoil()
    {
        float recX = Random.Range(minX, maxX);
        float recY = Random.Range(minY, maxY);
        camera_fps.transform.localRotation = Quaternion.Euler(rot.x - recY, rot.y + recX, rot.z);
    }
   IEnumerator dusman_hit_aldi_ct()
    {
        hit_sound.SetActive(true);
        dusman_anim.SetBool("hit", true);
        crosshair_image.color = Color.red;
        yield return new WaitForSeconds(0.3f);
        crosshair_image.color = Color.green;
        dusman_anim.SetBool("hit", false);
        hit_sound.SetActive(false);
    }
}

setting sicript.cs (ayarlar)

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Audio;
using UnityEngine.UI; 
public class SettingsScript : MonoBehaviour
{
    public Slider SLÝDER;
    public int Kalite_Ýndex; 
    public void Start()
    {
        LoadAudio();
        Load_Kalite();
    }
    public void SetVolume(float volume)
    {
        AudioListener.volume = volume;
        SaveAudio();
    }
    public void SaveAudio()
    {
        PlayerPrefs.SetFloat("SesKayit", AudioListener.volume);
    }
    public void LoadAudio()
    {
        if(PlayerPrefs.HasKey("SesKayit"))
        {
            AudioListener.volume = PlayerPrefs.GetFloat("SesKayit");
            SLÝDER.value = PlayerPrefs.GetFloat("SesKayit");
        }
        else
        {
            PlayerPrefs.SetFloat("SesKayit", 0.2f);
            AudioListener.volume = PlayerPrefs.GetFloat("SesKayit");
            SLÝDER.value = PlayerPrefs.GetFloat("SesKayit");
        }
      
    }
     
   
    public void Load_Kalite()
    {
        if (PlayerPrefs.HasKey("Grafik"))
        {
            QualitySettings.SetQualityLevel(PlayerPrefs.GetInt("Grafik"));
        }
        else
        {
            PlayerPrefs.SetFloat("Grafik", 0);
            QualitySettings.SetQualityLevel(PlayerPrefs.GetInt("Grafik"));
        }
    }
    public void Dusuk_Grafik()
    {
        Kalite_Ýndex = 1;
        PlayerPrefs.SetInt("Grafik", Kalite_Ýndex);
        Debug.Log(PlayerPrefs.GetInt("Grafik"));
        QualitySettings.SetQualityLevel(PlayerPrefs.GetInt("Grafik"));
    }
    public void Orta_Grafik()
    {
        Kalite_Ýndex = 2;
        PlayerPrefs.SetInt("Grafik", Kalite_Ýndex);
        Debug.Log(PlayerPrefs.GetInt("Grafik"));
        QualitySettings.SetQualityLevel(PlayerPrefs.GetInt("Grafik"));
    }
    public void Yuksek_Grafik()
    {
        Kalite_Ýndex = 3; 
        PlayerPrefs.SetInt("Grafik", Kalite_Ýndex);
        Debug.Log(PlayerPrefs.GetInt("Grafik"));
        QualitySettings.SetQualityLevel(PlayerPrefs.GetInt("Grafik"));
    }
  
    public void Ultra_Grafik()
    {
        Kalite_Ýndex = 4;
        PlayerPrefs.SetInt("Grafik", Kalite_Ýndex);
        Debug.Log(PlayerPrefs.GetInt("Grafik"));
        QualitySettings.SetQualityLevel(PlayerPrefs.GetInt("Grafik"));
    }
}

silahlar.cs

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
public class Silahlar : MonoBehaviour
{
    public string anaSilah;
    void Start()
    {
        
    }
    // Update is called once per frame
    void Update()
    {
        
    }
}

keskin nişancı saldırısı.cs

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using TMPro;
public class SniperSaldiri : MonoBehaviour
{
    public GameObject sniper_el; // susturucu el olan objenin kendisi
    public Animator sniper_anim; // susturucu elin animatoru
    // mermi sistemi 
    public float mermi_Sayisi = 12f; // baslangýctaki mermi sayýmýzý belirledik 
    public Button atesetme_button; // bu butonu tanýmlama amacým: eger silah reload durumuna girerse mermi saysýnýn azlmasýna engel olmaktýr
    public Button Reload_button;
    public Button slot_susturucu_buton, slot_sniper_buton, slot_pompali_buton, slot_keles_buton,bicak_saldiri_buton;
    public TextMeshProUGUI mermisayi_text; // bu bize ekranda kaç mermi kaldýðýmýzý göstericek 
    // ates sistemi gerekilenler
    public GameObject atessesi_sniper;
    public GameObject reloadesei_sniper;
    public GameObject muzzleflash_sniper;
    public GameObject mermifirlama_sniper;
    public GameObject mermiizi_sniper; // mermi efecti calýstýrýr
    // ates sistemi mekanigi
    public float mesafe = 100f;
    public Camera fpsCam;
    // recoil efekt sistemi
    public float minX, maxX;
    public float minY, maxY;
    public Transform camera_fps;
    public Vector3 rot;
    // karakter hareketi ile alakalý kodlar 
    KarakterHareket player_Hareket;
    // dusmana hit ile alakali icerikler
    Animator dusman_anim;
    Level1SpawnManager spawn_manager;
    public GameObject hit_sound; // hasar verme sesi 
    public Image crosshair_image;
    DefenderAi defender_ai;
    public void Sniperel_Ates()
    {
        AtesMekanigi();
        recoil();
        StartCoroutine("muzzle_flash_control");
        StartCoroutine("atesetme"); // bu fonksiyon bize ates etme butonuna týkladýgýmýzda ates etmeyý saglar 
    }
    public void Sniper_Reload()
    {
        if (mermi_Sayisi < 12)
        {
            StartCoroutine("reload"); // bu fonksiyon bize silahýn reload durumuna girmesini saglar 
        }
    }
    public void Start()
    {
        PlayerPrefs.GetFloat("sniper_mermi");
        player_Hareket = GameObject.Find("Player").GetComponent<KarakterHareket>();
        spawn_manager = GameObject.Find("SpawnManager").GetComponent<Level1SpawnManager>();
    }
    public void Update()
    {
        sniper_el.transform.localPosition = new Vector3(50f, -1.411f, 0.6870003f);
        PlayerPrefs.SetFloat("sniper_mermi", mermi_Sayisi); // tabanca mermisini kayýt ettik
        mermisayi_text.text = PlayerPrefs.GetFloat("sniper_mermi") + "" + "/" + "" + "999"; // ana sayfada kalan mermi sayýmýzý gösterdik 
        #region oto_reload 
        if (mermi_Sayisi <= 0) // bu blok bize mermi 0 dan düþük oldugunda oto reload etmesini saðlar
        {
            StartCoroutine("reload");
        }
        #endregion
        #region recoil efekt hesaplamalari // recoil efektleri
        rot = camera_fps.transform.localRotation.eulerAngles; // recoil efekt için kameranýn rotasyonunu kaydettik
        if (rot.x != 0 || rot.y != 0)
        {
            camera_fps.transform.localRotation = Quaternion.Slerp(camera_fps.transform.localRotation, Quaternion.Euler(0, 0, 0), Time.deltaTime * 3); // bu kod recoil olduktan sonra kameranýn yavas tavas asagý ýnmesýný saglar
        }
        #endregion
    }
    IEnumerator atesetme() // ates etme olaylarýný bir numerator ýle döndürdüm
    {
        sniper_anim.SetBool("Ýdle_sniper", true);
        sniper_anim.SetBool("AtesEtme_sniper", true);
        atesetme_button.enabled = false; // ust uste týklayýp anýmasyon cakýsmasýný engellemek istedim
        player_Hareket.Silah_Tetiklendi = true;
        mermifirlama_sniper.SetActive(true);
        atessesi_sniper.SetActive(true);
        mermi_Sayisi--;
        yield return new WaitForSeconds(1f);
        player_Hareket.Silah_Tetiklendi = false; 
        mermifirlama_sniper.SetActive(false);
        atessesi_sniper.SetActive(false);
        atesetme_button.enabled = true; // butonu tekrardan aktifleþtirerek sýkabilmesini saðladým 
        sniper_anim.SetBool("AtesEtme_sniper", false);
    }
    IEnumerator reload() // reload olaylarýný bir numerator ile döndürdüm
    {
        sniper_el.transform.rotation = Quaternion.Euler(10f, 10f, 10f);
        sniper_anim.SetBool("Ýdle_sniper", true);
        sniper_anim.SetBool("AtesEtme_sniper", false);
        sniper_anim.SetBool("Reload_sniper", true);
        atesetme_button.enabled = false;
        Reload_button.enabled = false;
        mermi_Sayisi = 0f;
        reloadesei_sniper.SetActive(true);
        slot_keles_buton.enabled = false;
        slot_pompali_buton.enabled = false;
        slot_sniper_buton.enabled = false;
        slot_susturucu_buton.enabled = false;
        bicak_saldiri_buton.enabled = false;
        yield return new WaitForSeconds(1.5f);
        reloadesei_sniper.SetActive(false);
        Reload_button.enabled = true;
        mermi_Sayisi = 12f;
        atesetme_button.enabled = true;
        sniper_anim.SetBool("Reload_sniper", false);
        sniper_anim.SetBool("Ýdle_sniper", true);
        slot_keles_buton.enabled = true;
        slot_pompali_buton.enabled = true;
        slot_sniper_buton.enabled = true;
        slot_susturucu_buton.enabled = true;
        bicak_saldiri_buton.enabled = true; 
    }
    IEnumerator muzzle_flash_control()
    {
        muzzleflash_sniper.SetActive(true);
        yield return new WaitForSeconds(0.1f);
        muzzleflash_sniper.SetActive(false);
    }
    void AtesMekanigi()
    {
        RaycastHit hit;
        if (Physics.Raycast(fpsCam.transform.position, fpsCam.transform.forward, out hit, 25f))
        {
            Debug.Log(hit.transform.name);
            dusman_anim = hit.transform.gameObject.GetComponent<Animator>();
            defender_ai = hit.transform.gameObject.GetComponent<DefenderAi>();
            GameObject mermi_izi = Instantiate(mermiizi_sniper, hit.point, Quaternion.LookRotation(hit.normal));
            #region mermi izi siler 
            Destroy(mermi_izi, 2f);
            #endregion
            if (hit.transform.gameObject.tag == "ct" && spawn_manager.terorist_Secildi == true)
            {
                StartCoroutine("dusman_hit_aldi_ct");
                defender_ai.CanAzalt(80);
            }
        }
    }
    public void recoil()
    {
        float recX = Random.Range(minX, maxX);
        float recY = Random.Range(minY, maxY);
        camera_fps.transform.localRotation = Quaternion.Euler(rot.x - recY, rot.y + recX, rot.z);
    }
    IEnumerator dusman_hit_aldi_ct()
    {
        hit_sound.SetActive(true);
        dusman_anim.SetBool("hit", true);
        crosshair_image.color = Color.red;
        yield return new WaitForSeconds(0.3f);
        crosshair_image.color = Color.green;
        dusman_anim.SetBool("hit", false);
        hit_sound.SetActive(false);
    }
}

keskin nişancı kapsamı

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using TMPro;
public class SniperScope : MonoBehaviour
{
    public GameObject scope_Panel;
    public Camera fps_Camera;
    public slotkontrol slotkontrol_kod; 
    public bool acabilir;
    public GameObject sniperscope_kapatBt,etkilesim_bt;
    void Start()
    {
        scope_Panel.SetActive(false);
       
    }
    public void Update()
    {
        acabilir = slotkontrol_kod.sniper_aktif;
        Debug.Log(acabilir);
    }
    public void ScopePanel_Ac()
    {
        if(acabilir == true)
        {
            scope_Panel.SetActive(true);
            fps_Camera.fieldOfView = 8f;
            etkilesim_bt.SetActive(false);
            sniperscope_kapatBt.SetActive(true);
        }
    }
    public void ScopePanel_Kapat()
    {
        scope_Panel.SetActive(false);
        fps_Camera.fieldOfView = 50f;
        etkilesim_bt.SetActive(true);
        sniperscope_kapatBt.SetActive(false);
    }
}

susturucuSaldırısı.cs

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using TMPro; 
public class SusturucuSaldiri : MonoBehaviour
{
    public GameObject susturucu_el; // susturucu el olan objenin kendisi
    public Animator susturucuel_anim; // susturucu elin animatoru
    // mermi sistemi 
    public float mermi_Sayisi = 12f; // baslangýctaki mermi sayýmýzý belirledik 
    public Button atesetme_button; // bu butonu tanýmlama amacým: eger silah reload durumuna girerse mermi saysýnýn azlmasýna engel olmaktýr
    public Button Reload_button;
    public Button slot_susturucu_buton, slot_sniper_buton, slot_pompali_buton, slot_keles_buton, bicak_saldiri_buton;
    public TextMeshProUGUI mermisayi_text; // bu bize ekranda kaç mermi kaldýðýmýzý göstericek 
    // ates sistemi gerekilenler
    public GameObject atessesi_tabanca;
    public GameObject reloadesei_tabanca; 
    public GameObject muzzleflash_tabanca;
    public GameObject mermifirlama_tabanca;
    public GameObject mermiizi_tabanca; // mermi efecti calýstýrýr
    // ates sistemi mekanigi
    
    public float mesafe = 100f;
    public Camera fpsCam;
    // camera shake ( sallanma efekti )
    public CameraShake cameraShake; // bomba patladýgýnda sallanma efekti icin yazýldý. Bombada sallanmasýný istiyorsan kullanýcaksýn 
    // recoil sistemi (silah ateþlendiðinde kameraya vurus hissiyatý katmak için yazýlan koddur)
    public float minX, maxX;
    public float minY, maxY;
    public Transform camera_fps;
    public Vector3 rot;
    // karakterýn yurume koduna erisme 
    KarakterHareket player_Hareket;
    // dusmana hit ile alakali icerikler
    Animator dusman_anim;
    Level1SpawnManager spawn_manager;
    public GameObject hit_sound; // hasar verme sesi 
    public Image crosshair_image;
    DefenderAi defender_ai;
    public void Susturucuel_Ates()
    {
        AtesMekanigi();
        recoil();
        StartCoroutine("muzzle_flash_control");
        StartCoroutine("atesetme"); // bu fonksiyon bize ates etme butonuna týkladýgýmýzda ates etmeyý saglar 
    }
    public void Susturucu_Reload()
    {
        if(mermi_Sayisi < 12)
        {
            StartCoroutine("reload"); // bu fonksiyon bize silahýn reload durumuna girmesini saglar 
        }
    }
    public void Start()
    {
        PlayerPrefs.GetFloat("tabanca_mermi");
        player_Hareket = GameObject.Find("Player").GetComponent<KarakterHareket>();
        spawn_manager = GameObject.Find("SpawnManager").GetComponent<Level1SpawnManager>();
    }
    public void Update()
    {
        PlayerPrefs.SetFloat("tabanca_mermi", mermi_Sayisi); // tabanca mermisini kayýt ettik
        mermisayi_text.text = PlayerPrefs.GetFloat("tabanca_mermi") + "" + "/" + "" + "999"; // ana sayfada kalan mermi sayýmýzý gösterdik 
        #region oto_reload 
        if (mermi_Sayisi <= 0) // bu blok bize mermi 0 dan düþük oldugunda oto reload etmesini saðlar
        {
            StartCoroutine("reload");
        }
        #endregion
        #region recoil efekt hesaplamalari // recoil efektleri
        rot = camera_fps.transform.localRotation.eulerAngles; // recoil efekt için kameranýn rotasyonunu kaydettik
        if(rot.x != 0 ||rot.y != 0)
        {
            camera_fps.transform.localRotation = Quaternion.Slerp(camera_fps.transform.localRotation, Quaternion.Euler(0, 0, 0), Time.deltaTime * 3); // bu kod recoil olduktan sonra kameranýn yavas tavas asagý ýnmesýný saglar
        }
        #endregion
    }
    IEnumerator atesetme() // ates etme olaylarýný bir numerator ýle döndürdüm
    {
        
        susturucuel_anim.SetBool("idle", true);
        susturucuel_anim.SetBool("ates_susturucu", true);
        atesetme_button.enabled = false; // ust uste týklayýp anýmasyon cakýsmasýný engellemek istedim
        player_Hareket.Silah_Tetiklendi = true;
        mermifirlama_tabanca.SetActive(true);
        atessesi_tabanca.SetActive(true);
        mermi_Sayisi--;
        yield return new WaitForSeconds(0.4f);
        player_Hareket.Silah_Tetiklendi = false;
        mermifirlama_tabanca.SetActive(false);
        atessesi_tabanca.SetActive(false);
        atesetme_button.enabled = true; // butonu tekrardan aktifleþtirerek sýkabilmesini saðladým 
        susturucuel_anim.SetBool("ates_susturucu", false);
    }
    IEnumerator reload() // reload olaylarýný bir numerator ile döndürdüm
    {
        susturucuel_anim.SetBool("idle", true);
        susturucuel_anim.SetBool("ates_susturucu", false);
        susturucuel_anim.SetBool("reload_susturucu", true);
        atesetme_button.enabled = false;
        Reload_button.enabled = false; 
        mermi_Sayisi = 0f;
        reloadesei_tabanca.SetActive(true);
        slot_keles_buton.enabled = false;
        slot_pompali_buton.enabled = false;
        slot_sniper_buton.enabled = false;
        slot_susturucu_buton.enabled = false;
        bicak_saldiri_buton.enabled = false;
        yield return new WaitForSeconds(1.6f);
        reloadesei_tabanca.SetActive(false);
        Reload_button.enabled = true;
        mermi_Sayisi = 12f;
        atesetme_button.enabled = true;
        susturucuel_anim.SetBool("reload_susturucu", false);
        slot_keles_buton.enabled = true;
        slot_pompali_buton.enabled = true;
        slot_sniper_buton.enabled = true;
        slot_susturucu_buton.enabled = true;
        bicak_saldiri_buton.enabled = true;
    }
    IEnumerator muzzle_flash_control()
    {
        muzzleflash_tabanca.SetActive(true);
        yield return new WaitForSeconds(0.1f);
        muzzleflash_tabanca.SetActive(false);
    }
    void AtesMekanigi()
    {
        RaycastHit hit;
        if (Physics.Raycast(fpsCam.transform.position, fpsCam.transform.forward, out hit, 50f))
        {
            Debug.Log(hit.transform.name);
            dusman_anim = hit.transform.gameObject.GetComponent<Animator>();
            defender_ai = hit.transform.gameObject.GetComponent<DefenderAi>();
            GameObject mermi_izi =   Instantiate(mermiizi_tabanca, hit.point, Quaternion.LookRotation(hit.normal));
            #region mermi izi siler 
            Destroy(mermi_izi, 2f);
            #endregion
            if (hit.transform.gameObject.tag == "ct" && spawn_manager.terorist_Secildi == true)
            {
                StartCoroutine("dusman_hit_aldi_ct");
                defender_ai.CanAzalt(20);
            }
        }
    }
    public void recoil()
    {
        float recX = Random.Range(minX, maxX);
        float recY = Random.Range(minY, maxY);
        camera_fps.transform.localRotation = Quaternion.Euler(rot.x - recY, rot.y + recX, rot.z);
    }
    IEnumerator dusman_hit_aldi_ct()
    {
        hit_sound.SetActive(true);
        dusman_anim.SetBool("hit", true);
        crosshair_image.color = Color.red;
        yield return new WaitForSeconds(0.3f);
        crosshair_image.color = Color.green;
        dusman_anim.SetBool("hit", false);
        hit_sound.SetActive(false);
    }
}

zamanlayıcı.cs

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using TMPro; 
public class Timer : MonoBehaviour
{
    [SerializeField] private int _simdikiZaman;
    [SerializeField] private int _DurationTime;
    [SerializeField] private TextMeshProUGUI _TimerText;
    public bool zaman_kontrol; 
    void Start()
    {
        zaman_kontrol = false; 
        _simdikiZaman = _DurationTime;
        _TimerText.text = _simdikiZaman.ToString();
        
    }
    public void LateUpdate()
    {
        StartCoroutine(ZamanAzalt());
    }
    private IEnumerator ZamanAzalt()
    {
        while(_simdikiZaman >= 0 && zaman_kontrol == true )
        {
            _TimerText.text = _simdikiZaman.ToString();
            yield return new WaitForSeconds(1f);
            _simdikiZaman -= 1;
           
        }
        yield return null; 
    }
    
}

silahSallamak.cs

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
public class WeaponSway : MonoBehaviour
{
    [Header("Sway ayarlarý")]
    [SerializeField] private float smooth;
    [SerializeField] private float sway_multiplier;
    public FloatingJoystick joystick; 
    private void Update()
    {
        float mouseX = joystick.Horizontal * sway_multiplier;
        float mouseY = joystick.Vertical * sway_multiplier;
        Quaternion rotationX = Quaternion.AngleAxis(-mouseY, Vector3.right);
        Quaternion rotationY = Quaternion.AngleAxis(mouseX, Vector3.up);
        Quaternion targetRotation = rotationX * rotationY;
        transform.localRotation = Quaternion.Slerp(transform.localRotation, targetRotation, smooth * Time.deltaTime);
    }
}

ekransahnesi yükleniyor

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.SceneManagement; 
public class loadingscreenscene : MonoBehaviour
{
    
    void Start()
    {
        Invoke("Loading_Sceene_Kapatma", 3f);
    }
    public void Loading_Sceene_Kapatma()
    {
        SceneManager.LoadScene("UÝ");
    }
    
    
}

panel yönetme.cs

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement; 
public class panelmanagement : MonoBehaviour
{
    public GameObject panelmenu, panelayarlar, panelgorev; 
    void Start()
    {
        panelmenu.SetActive(false);
        panelayarlar.SetActive(false);
        panelgorev.SetActive(false); 
    }
    public void panelmenu_ac()
    {
        Time.timeScale = 0; 
        panelmenu.SetActive(true);
        panelayarlar.SetActive(false);
        panelgorev.SetActive(false);
    }
    public void panelmenu_kapat()
    {
        Time.timeScale = 1; 
        panelmenu.SetActive(false);
        panelayarlar.SetActive(false);
        panelgorev.SetActive(false);
    }
    public void panelayarlar_Ac()
    {
        panelmenu.SetActive(false);
        panelayarlar.SetActive(true);
        panelgorev.SetActive(false);
    }
    public void panelayarlar_Kapat()
    {
        panelmenu.SetActive(true);
        panelayarlar.SetActive(false);
        panelgorev.SetActive(false);
    }
    public void panelgorev_Ac()
    {
        panelmenu.SetActive(false);
        panelayarlar.SetActive(false);
        panelgorev.SetActive(true);
    }
    public void panelgorev_Kapat()
    {
        panelmenu.SetActive(false);
        panelayarlar.SetActive(false);
        panelgorev.SetActive(false);
    }
    public void anamenu_don()
    {
        SceneManager.LoadScene("UÝ");
        Time.timeScale = 1; 
    }
    
}

panel yöneticisi.cs

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI; 
public class panelmanager : MonoBehaviour
{
    public GameObject LevelSecimPanel;
    void Start()
    {
        LevelSecimPanel.SetActive(false);
    }
    public void LevelSecimAc()
    {
        LevelSecimPanel.SetActive(true); 
    }
    public void LevelSecimKapat()
    {
        LevelSecimPanel.SetActive(false); 
    }
    public void OyundanCik()
    {
        Application.Quit();
    }
    // Hover Efectler
    public void OnMouseOver()
    {
        
    }
    public void OnMouseExit()
    {
        
    }
}

kilitkontrolü.cs

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using TMPro; 
public class slotkontrol : MonoBehaviour
{
    public GameObject pompali_el, susturucu_el, sniper_el, keles_el;
    public GameObject SusturucuControlManagement, PompaliControlManagement, KelesControlManagement, SniperControlManagement; 
    public GameObject susturucuates_buton,pompaliates_buton,kelesates_buton,sniperates_buton;
    public GameObject susturucureload_buton, pompalireload_buton, kelesreload_buton, sniperreload_buton;
    public bool sniper_aktif = false;
    public Button nisan_alma_bt;
    public GameObject scope_panel;
    public Camera fps_Camera;
    // not : ates etme butonunun onclick kýsýmýna toplamda 4 adet magamentleri sýrayla yerleþtiricen
    
    public void Start()
    {
        susturucu_el.SetActive(false);
        sniper_el.SetActive(false);
        pompali_el.SetActive(false);
        keles_el.SetActive(true);
        SusturucuControlManagement.SetActive(false);
        PompaliControlManagement.SetActive(false);
        KelesControlManagement.SetActive(true);
        SniperControlManagement.SetActive(false);
        susturucuates_buton.SetActive(false);
        pompaliates_buton.SetActive(false);
        sniperates_buton.SetActive(false);
        kelesates_buton.SetActive(true);
        susturucureload_buton.SetActive(false);
        pompalireload_buton.SetActive(false);
        sniperreload_buton.SetActive(false);
        kelesreload_buton.SetActive(true);
        sniper_aktif = false;
        nisan_alma_bt.enabled = false;
    }
    
    public void Tabanca()
    {
        susturucu_el.SetActive(true);
        sniper_el.SetActive(false);
        pompali_el.SetActive(false);
        keles_el.SetActive(false);
        // control managementler scriptleri slot managementini kontrol etmemiz için kuruldu. Temel mantýgý oyuncunun içinde 4 tane ayrý obje olusturup silahlarýn scriptlerine silahlarýn objesine göre koymaktýr
        SusturucuControlManagement.SetActive(true);
        PompaliControlManagement.SetActive(false);
        KelesControlManagement.SetActive(false);
        SniperControlManagement.SetActive(false);
        // silahlara göre ates butonlarýn acýlmasý ýcýn 4 temel silaha ayrý ayrý butonlar tanýmladým
        susturucuates_buton.SetActive(true);
        pompaliates_buton.SetActive(false);
        sniperates_buton.SetActive(false);
        kelesates_buton.SetActive(false);
        // her silahýn ayrý bir reload sistemi olacagýndan her birine ayrý buton tanýmlamayý uygun gördüm
        susturucureload_buton.SetActive(true);
        pompalireload_buton.SetActive(false);
        sniperreload_buton.SetActive(false);
        kelesreload_buton.SetActive(false);
        sniper_aktif = false;
        nisan_alma_bt.enabled = false;
        scope_panel.SetActive(false);
        fps_Camera.fieldOfView = 50f;
    }
    public void Pompali()
    {
        susturucu_el.SetActive(false);
        sniper_el.SetActive(false);
        pompali_el.SetActive(true);
        keles_el.SetActive(false);
        SusturucuControlManagement.SetActive(false);
        PompaliControlManagement.SetActive(true);
        KelesControlManagement.SetActive(false);
        SniperControlManagement.SetActive(false);
        susturucuates_buton.SetActive(false);
        pompaliates_buton.SetActive(true);
        sniperates_buton.SetActive(false);
        kelesates_buton.SetActive(false);
        susturucureload_buton.SetActive(false);
        pompalireload_buton.SetActive(true);
        sniperreload_buton.SetActive(false);
        kelesreload_buton.SetActive(false);
        sniper_aktif = false;
        nisan_alma_bt.enabled = false;
        scope_panel.SetActive(false);
        fps_Camera.fieldOfView = 50f;
    }
    public void Sniper()
    {
        susturucu_el.SetActive(false);
        sniper_el.SetActive(true);
        pompali_el.SetActive(false);
        keles_el.SetActive(false);
        SusturucuControlManagement.SetActive(false);
        PompaliControlManagement.SetActive(false);
        KelesControlManagement.SetActive(false);
        SniperControlManagement.SetActive(true);
        susturucuates_buton.SetActive(false);
        pompaliates_buton.SetActive(false);
        sniperates_buton.SetActive(true);
        kelesates_buton.SetActive(false);
        susturucureload_buton.SetActive(false);
        pompalireload_buton.SetActive(false);
        sniperreload_buton.SetActive(true);
        kelesreload_buton.SetActive(false);
        sniper_aktif = true;
        nisan_alma_bt.enabled = true;
        fps_Camera.fieldOfView = 50f;
    }
    public void Keles()
    {
        susturucu_el.SetActive(false);
        sniper_el.SetActive(false);
        pompali_el.SetActive(false);
        keles_el.SetActive(true);
        SusturucuControlManagement.SetActive(false);
        PompaliControlManagement.SetActive(false);
        KelesControlManagement.SetActive(true);
        SniperControlManagement.SetActive(false);
        susturucuates_buton.SetActive(false);
        pompaliates_buton.SetActive(false);
        sniperates_buton.SetActive(false);
        kelesates_buton.SetActive(true);
        susturucureload_buton.SetActive(false);
        pompalireload_buton.SetActive(false);
        sniperreload_buton.SetActive(false);
        kelesreload_buton.SetActive(true);
        sniper_aktif = false;
        nisan_alma_bt.enabled = false;
        scope_panel.SetActive(false);
        fps_Camera.fieldOfView = 50f;
    }
}

uyanış.cs

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;
using TMPro;
public class uyanis : MonoBehaviour
{
    public void Level1Ac()
    {
        SceneManager.LoadScene("level1");
    }
   
    
}
