# 2-3-project

기초 게임타이틀 UI및 캐릭터 선택 UI 제작
![image](https://github.com/user-attachments/assets/555aa882-1efa-4926-a365-2a097a59c2c7)
기초 게임타이틀 UI
Start 버튼 클릭으로 캐릭터 선택 UI로 이동
Exit 버튼으로 게임 종료 (Online 미완성)
![image](https://github.com/user-attachments/assets/57fe23d4-050c-424a-aa3c-f4176fc9718b)
캐릭터 선택 UI에서 각 캐릭터의 직업, 무기 선택 후 게임 시작버튼으로 시작, cancel버튼으로 타이틀로 돌아가는 방식
![image](https://github.com/user-attachments/assets/bf8a4847-1f86-4f28-9887-1e86fbdb2ffc)

UI컨트롤러 스크립트를 통해 기능하며 세팅상황은 아래와 같다.
![image](https://github.com/user-attachments/assets/33cca521-5cdf-4446-b174-632caedd4984)

전체적인 UI Controller 스크립트 내용

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.SceneManagement;
using UnityEngine.UI;
using TMPro; // TextMeshPro 사용을 위한 네임스페이스 추가

public enum OccupationList {Warrior, Archer, Wizard }
public enum WeaponList {Sword, Bow, Stuff }
public class UIController : MonoBehaviour
{
    public Canvas TitleCanvas;
    public Canvas ReadyCanvas;
    public List<GameObject> TitleButtons = new List<GameObject>(); // 타이틀 버튼 리스트
    public TextMeshProUGUI occupationText; // 직업을 표시할 TextMeshProUGUI
    public TextMeshProUGUI weaponText;     // 무기를 표시할 TextMeshProUGUI

    public int count = 0;
    // Start is called before the first frame update
    public void GoReady()
    {
        CheckTitleButtons(false);
        ReadyCanvas.gameObject.SetActive(true);
    }

    public void GameStart()
    {
        // 원하는 씬 이름 또는 빌드 인덱스로 씬 변경
        SceneManager.LoadScene("Test"); // "GameScene"을 원하는 씬 이름으로 변경  
    }

    public void Cancel()
    {
        CheckTitleButtons(true);
        ReadyCanvas.gameObject.SetActive(false);
    }

    private void CheckTitleButtons(bool isActive)
    {
        foreach (GameObject button in TitleButtons)
        {
            button.SetActive(isActive); // 버튼 비활성화
        }
    }

    public void Online()
    {
        Debug.Log("아직 개발되지 않았습니다.");
    }

    public void Exit()
    {
        // 유니티 실행 종료
        Application.Quit();

        // 에디터에서 테스트할 때는 아래 코드도 필요합니다.
        #if UNITY_EDITOR
        UnityEditor.EditorApplication.isPlaying = false;
        #endif
    }
    // 직업 및 무기 업데이트
    private void UpdateOccupationAndWeapon()
    {
        OccupationList currentOccupation = (OccupationList)count;
        WeaponList currentWeapon = (WeaponList)count;

        occupationText.text =  currentOccupation.ToString();
        weaponText.text = currentWeapon.ToString();
    }
    // count 증가
    public void IncrementCount()
    {
        count++;
        if (count > 2)
        {
            count = 0; // 2 이상이면 다시 0으로 순환
        }
        UpdateOccupationAndWeapon();
    }
    // count 감소
    public void DecrementCount()
    {
        count--;
        if (count < 0)
        {
            count = 2; // 0 이하이면 다시 2로 순환
        }
        UpdateOccupationAndWeapon();
    }

    void Start()
    {
        ReadyCanvas.gameObject.SetActive(false);
        UpdateOccupationAndWeapon();
    }
    // Update is called once per frame
    void Update()
    {

    }
}
