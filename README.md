# Swallowtail_Butterfly_VR

<h2> 01 Project description </h2>
How would you feel if you were a butterfly seeking to live in a desperate world of chaos? Inspired by the Japanese film 'Swallowtail Butterfly' and the game Cyberpunk 2077, I created a VR game that requires users to survive through three artificial crises. It allows people to think about what they truly need in a dissipated world. 


<h2>02 How to install and run the project </h2>
Download the .apk file and run it in your PICO VR deive

<h2>03 Diagram of the entire system </h2>
![diagram](useflow_sw.png)

 
<h2>04 Functionality Specification</h2>

<h3>Level 1</h3>
The task of this level is to escape from the falling down neon billboard.

<h3> Dorping controll</h5>

> The sample files for this chapter are located in "Assets/Scripts/Level1/DropGo.cs".<br>




Execution code

```csharp
// The file name and class name must match.
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class DropGo : MonoBehaviour
{
// the bool type variable is set to check the droping status of the object;
    bool IsDrop = false;  
// define the audio clip of the falling down sound.
    public AudioClip dropClip; 
    
    void Start()
    {
        
    }

 
    void Update()
    {
    // when the user move to the position where the dropping action will be awake.
        if (transform.GetComponent<Rigidbody>().useGravity&&!IsDrop) 
        {
            IsDrop = true;
            Invoke("ChangeDropGoTag", 5f);
        }
    }
    
    // change the tag of the dropping status to make the object falling down action can be switched. 

   void ChangeDropGoTag()
    {
        transform.GetComponent<Rigidbody>().useGravity = false;
        transform.tag = "Untagged";
        transform.GetComponent<Rigidbody>().isKinematic = true;
    }

// play the audio when the collison happend.

    private void OnCollisionEnter(Collision collision)
    {
        transform.GetComponent<AudioSource>().PlayOneShot(dropClip);
    }
}

```

<br>

<h3> Dorping Trigger</h5>

> The sample files for this chapter are located in "Assets/Scripts/Level1/ObjDropTrigger.cs".<br>


Execution code

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class ObjDropTrigger : MonoBehaviour
{
    public Rigidbody DropRig;

    // Update is called once per frame
    void Update()
    {

    }
// enable the gravity of the neon board object when the user arrive at the danger position.
    private void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("Player"))
        {
            DropRig.isKinematic = false;
            DropRig.useGravity = true;
            Destroy(gameObject);
        }
    }
}

```

<br>


<h3>Level 2</h3>
The task of this level is to avoid shooting by the helicopter.

<h3> Bullet controll</h5>

> The sample files for this chapter are located in "Assets/Scripts/Level2/Bullet.cs".<br>

Execution code

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Bullet : MonoBehaviour
{

    // Start is called before the first frame update
    void Start()
    {

        Destroy(gameObject,2);
    }

    
    void Update()
    {
        //Translate(new Vector3(0,0,transform.localPosition.z) *50f * Time.deltaTime);
    }

    private void OnTriggerEnter(Collider other)
    {
    //
            Debug.Log("Hit successully");
        if (other.CompareTag("Player"))
        {
            other.GetComponent<PlayerCtrl>().BulletHitChangeHp(-20);
            Destroy(gameObject);
        }
    }

    private void OnCollisionEnter(Collision collision)
    {

    }
}

```

<br>

<h3> SpawnBullets</h5>

> The sample files for this chapter are located in "Assets/Scripts/Level1/SpawnBullets.cs".<br>


Execution code

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class SpawnBullets : MonoBehaviour
{
    public GameObject bulletPre;
    public Transform bulletSpawnPos;

    float timer = 1;
    public int intvalTime;

    AudioSource AS;
    public AudioClip ShootClips;
    // Start is called before the first frame update
    void Start()
    {
        AS = transform.parent.GetComponent<AudioSource>();
    }

    // Update is called once per frame
    void Update()
    {
        int layerMask = 8;
        RaycastHit hit;
        
        //set the bulletspawn as the raycast
        Physics.Raycast(bulletSpawnPos.position, bulletSpawnPos.TransformDirection(Vector3.forward),out hit/*,layerMask*/);
        //Vector3 temp = bulletSpawnPos.TransformDirection(Vector3.forward) +new Vector3(0, 0, 10);
        //Debug.DrawLine(bulletSpawnPos.position,temp, Color.red);
        //Debug.Log(hit.transform.name);
        
        // when the hit raycast overlaps the player, start the shooting effects, play the shooting sound. 
        if (hit.transform.name.Equals("Player"))
        {
            bulletSpawnPos.GetComponent<Animator>().enabled = false;
            timer += Time.deltaTime;
            if (timer >= intvalTime)
            {
                AS.PlayOneShot(ShootClips);
                GameObject bullet = Instantiate(bulletPre, bulletSpawnPos.position, Quaternion.Euler(Vector3.zero));
                //bullet.transform.SetParent(bulletSpawnPos);
                //bullet.transform.localRotation = Quaternion.Euler(Vector3.zero);
                bullet.transform.SetParent(transform);
                bullet.transform.LookAt(hit.transform);
                bullet.GetComponent<Rigidbody>().AddForce(bullet.transform.forward * 500);
                timer = 0;
            }

        }
        else
        {
            bulletSpawnPos.GetComponent<Animator>().enabled = true;
            timer = 1;
        }
        
    }
}

```

<br>

<h3>Level 3</h3>
The task of this level is to avoid striking by a car.

<h3> Car controll</h5>

> The sample files for this chapter are located in "Assets/Scripts/Level2/CarCtrl.cs".<br>

Execution code

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

//define two different cartypes;

public enum CarType
{
    Pos1,
    Pos2,
}
public class CarCtrl : MonoBehaviour
{
    public int MoveDir;
    [SerializeField]
    private GameObject Player;

    public CarType carType;
    private void Awake()
    {
        if (carType== CarType.Pos1)
        {
            Destroy(gameObject, 20);
        }
        else if (carType == CarType.Pos2)
        {
            Destroy(gameObject, 40);
        }
    }
    void Start()
    {
        Player = PlayerCtrl.Instance.gameObject;
        transform.localScale = Vector3.one;
    }
    
    // controll the car moving direction.
    void Update()
    {
        if (carType == CarType.Pos1)
        {
            transform.Translate(transform.TransformDirection(Vector3.right) * MoveDir * 10 * Time.deltaTime);
        }
        else if (carType == CarType.Pos2)
        {
            transform.Translate(transform.TransformDirection(Vector3.forward) * MoveDir * 10 * Time.deltaTime);
        }
    }

    private void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("Player"))
        {
            Player.tag = "Untagged";

            Player.GetComponent<PlayerCtrl>().CarHitChangeHp(-20);
            Invoke("AfterCrash", 3);
        }
    }
    void AfterCrash()
    {
        Player.tag = "Player";
        Player.GetComponent<PlayerCtrl>().HideScreenBlackGo();
        Destroy(gameObject);
    }
}


```

<br>

<h3> Spawn Car</h5>

> The sample files for this chapter are located in "Assets/Scripts/Level1/SpawnCar.cs".<br>


Execution code

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class SpawnCar : MonoBehaviour
{
    public Transform SpawnPos1;
    public GameObject[] CarPre;
    float timer = 5;
    public float IntvalTime;

    public Transform SpawnPos2;
    public GameObject[] CarPre2;
    float timer2 = 30;
    public float IntvalTime2;
    // Start is called before the first frame update
    void Start()
    {
        
    }

    // To create more and more cars
    void Update()
    {
        timer += Time.deltaTime;
        if (timer>=IntvalTime)
        {
            Instantiate(CarPre[Random.Range(0, CarPre.Length)],SpawnPos1.position,Quaternion.Euler(new Vector3(0,-90,0)),transform);
            timer = 0;
        }

        timer2 += Time.deltaTime;
        if (timer2 >= IntvalTime2)
        {
            Instantiate(CarPre2[Random.Range(0, CarPre2.Length)], SpawnPos2.position, Quaternion.Euler(Vector3.zero), transform);
            timer2 = 0;
        }
    }
}


```

<br>

<h3>Player Control</h3>

> The sample files for this chapter are located in "Assets/Scripts/Level2/PlayerCtrl.cs".<br>

Execution code

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;

public class PlayerCtrl : MonoBehaviour
{
    public static PlayerCtrl Instance;

    public Slider Slider_Hp;
    float Hp = 100;

    AudioSource PlayerAS;
    public AudioClip HitAC;

    public GameObject EndPanel;

    public GameObject ScreenBlackGo;
    public GameObject CarSpawnGo;
    public AudioSource CarBgAS;

    public GameObject Txt_DamageRemain;
    private void Awake()
    {
        Time.timeScale = 1;
    }
    // Start is called before the first frame update
    void Start()
    {
        Instance = this;
        PlayerAS = transform.GetComponent<AudioSource>();
    }

    // Update is called once per frame
    void Update()
    {

        EndPanel.transform.parent.GetComponent<Pvr_UIGraphicRaycaster>().enabled = true;
        EndPanel.transform.parent.GetComponent<GraphicRaycaster>().enabled = true;

        Slider_Hp.value = Hp;
        if (Hp<=0)
        {
        
            Txt_DamageRemain.SetActive(false); //hide the damage reminder text
            Time.timeScale = 0;
            EndPanel.SetActive(true); // show the game over UI panel
            ScreenBlackGo.SetActive(false); 
            EndPanel.transform.parent.GetComponent<Pvr_UIGraphicRaycaster>().enabled = true;
            EndPanel.transform.parent.GetComponent<GraphicRaycaster>().enabled = true;
        }
    }

    private void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("DropGo"))
        {
            Txt_DamageRemain.SetActive(true); // when the playeer hitted by the droping billboars, the text notification will show.
            Invoke("HideDamageRemain", 1);
            Hp -= 20;
            other.tag = "Untagged";
            //other.transform.GetComponent<Rigidbody>().isKinematic = true;
        }

        if (other.CompareTag("ShowCar"))
        {
            CarSpawnGo.SetActive(true);
            other.gameObject.SetActive(false);
            CarBgAS.enabled = true;
        }

        if (other.CompareTag("End"))
        {
            Txt_DamageRemain.SetActive(false);
            Time.timeScale = 0;
            EndPanel.transform.GetChild(0).GetComponent<Text>().text = "Game Over"; // when the HP used out, it wiil display the "game over"
            EndPanel.SetActive(true);
            ScreenBlackGo.SetActive(false);
            EndPanel.transform.parent.GetComponent<Pvr_UIGraphicRaycaster>().enabled = true;
            EndPanel.transform.parent.GetComponent<GraphicRaycaster>().enabled = true;
        }
    }

    public void BulletHitChangeHp(int value)
    {
        Txt_DamageRemain.SetActive(true);
        Invoke("HideDamageRemain", 1);
        Hp += value;
        PlayerAS.PlayOneShot(HitAC);
    }

    void HideDamageRemain()
    {
        Txt_DamageRemain.SetActive(false);
    }

    public void CarHitChangeHp(int value)
    {
        Txt_DamageRemain.SetActive(true);
        Invoke("HideDamageRemain", 1);
        ScreenBlackGo.SetActive(true);
        Hp += value;
    }

    public void GameAgain()
    {
        SceneManager.LoadScene("Level");
    }
    public void QuitGame()
    {
#if UNITY_EDITOR
        UnityEditor.EditorApplication.isPlaying = false;
#else
        Application.Quit();
#endif
    }

    public void HideScreenBlackGo()
    {
        ScreenBlackGo.SetActive(false);
    }
}


```

<br>


