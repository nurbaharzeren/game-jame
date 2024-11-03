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




