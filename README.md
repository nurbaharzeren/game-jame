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
