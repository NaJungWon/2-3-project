이번주 전체적인 계획
-
1.플레이어의 전체적인 기능 제작\
2.플레이어의 기본 공격 콤보, 강공격, 이동, 패링(방패막기), 대시, 스킬 구현\
3.플레이어의 구현된 기능에 애니메이션 삽입

전체적인 진행 현황
-
1. 플레이어의 전체적인 기능 제작\
  1.1- 기본 공격 (마우스 좌클릭)을 통한 공격 콤보 구현\
    \1.1.1- 자체 애니메이션 좌표 이동 문제로 애니메이션 좌표 이동 제한 
  1.2- 마우스 좌꾹 을 통한 강공격 기능 구현  
  1.3- leftshift입력을 통한 돌진 및 달리기 기능 구현\
    \1.3.1- 돌진 딜레이가 없어서 무한 돌진가능으로 인한 딜레이 구현    
  1.4- 마우스 우클릭을 통한 패링/방패막기 기능 구현\
    \1.4.1- 패링/방패막기 기능 사용동안 좌,우 화면 전환 및 타겟팅 제한

2. 전체적인 버그 수정내용\
    2.1- 기본 공격 입력 시 공격이 연달아서 나가는 버그 수정   
    2.2- 대시 등 다른 상태로 넘어갈시 공격이 캔슬됐다가 나중에 다시 나가는 버그 수정   
    2.3- 공격 상태 적용 미흡으로 인한 공격 중 이동 문제 해결

전체적으로 기본공격 콤보 부분에서 오류가 많이생겨서 많이 손봤다.

PlayerController 스크립트에 공격부분 메서드를 만들어 뒀는데, \
여러 차례 수정하다보니 결과적으로 아래와 같이 내용이 적어졌다.

void Attack()
{

    if (dfattackInput)
    {
        playerAnim.OnWeaponAttack();
    }
    if (stattackInput)
    {
        //playerAnim.OnWeaponAttack();
        attackTimer += Time.deltaTime;

        if (attackTimer >= StgAttackTime)
        {
            isStAttack = true;
        }
    }
    if(finattackInput)
    {
        attackTimer = 0f;
        isStAttack = false;
    }
}

이렇게 작성한 후 애니메이션을 관리하는 Player Anim스크립트에 

public void OnWeaponAttack()
{

    anim.SetTrigger("WeaponAttack");
}

내용을 작성하여 트리거로써 공격을 시행하도록 구현했다.

또한 공격 상태에 대한 정의 부분이 문제였는데, \
여기서 마우스 클릭시 isAttack등으로 bool값을 만들어 공격상태를 정의할 시\
이동하면서 공격할 때 마우스 클릭 중간중간에 캐릭터가 이동하는 문제가 있었다.

그래서 공격모션 애니메이션 리스트를 만들어서 리스트안의 애니메이션이 하나라도 실행중이라면 \
isAttack이 true가 되도록 하고 이동안 캐릭터의 이동이 불가능하도록 코드를 수정했다.

추가로 애니메이션 자체에 좌표이동이 적용되어 있었는데, 문제가 많아서 일단 좌표이동 안하도록 설정해 두었다.

캐릭터의 대쉬를 구현했는데 딜레이가 없자 무한하게 가속하는 문제가 생겨서 가속시간에 대한 딜레이를 만들어 적용시켰다.

void Dash()
{

    if (dashInput && canDash)
    {
        isRuned = true;
        if (!isDashed)
        {
            isDashed = true;
            //Debug.Log("대시 입력됨");
            // 정면으로 돌진 설정
            Vector3 forwardDash = transform.forward * dashPower;
            rb.AddForce(forwardDash, ForceMode.VelocityChange);

            StartCoroutine(ResetCooldown(dashCooldownTime));

        }
    }
    else
    {
        isDashed = false;
    }
}

IEnumerator ResetCooldown(float cooldownTime)
{

    canDash = false;
    //Debug.Log("쿨타임 대기중");
    yield return new WaitForSeconds(cooldownTime);
    canDash = true; // 대시 가능 여부를 true로 설정
    //Debug.Log("쿨타임 종료");
}

위 두 코드를 이용해서 canDash가 true일 때만 대쉬할 수 있도록 만들어 딜레이를 만들었다.\
대쉬는 Translate방식과 AddForce방식 둘다 사용해 봤는데 

Translate방식은 사용 미숙으로 카메라 움직임에 문제가 생겻고\
AddForce방식은 rigidbody설정 미숙으로 인한 어색함이 생겨서 일단 AddForce방식으로 구현했다.

방패막기/패링 부분에서 방패를 든 상태로 캐릭터가 그대로 회전하는 부분이 어색해서 \
일단 방패로 막는 모션을 취할 시 캐릭터의 회전이 불가능하도록 해두었다.
