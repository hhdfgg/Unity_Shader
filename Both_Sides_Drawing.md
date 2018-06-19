[【Unityシェーダ入門】ポリゴンの表面と裏面に別テクスチャを貼る](http://nn-hokuson.hatenablog.com/entry/2018/02/20/203511)
を参考にして分かったことと、スクリプト一部を解説



# スクリプトの一部解説

```cs
			fixed4 frag(v2f i, fixed facing : VFACE) : SV_Target
			{
				return (facing > 0 ) ? tex2D(_MainTex,i.uv) : tex2D(_MainTex2,i.uv);

				/*
					if(facing > 0)
					{
						tex2D(_MainTex, i.uv);
					}
					else
					{
						tex2D(_MainTex2, i.uv);
					}
					を略した書き方が
					return (facing > 0 ) ? tex2D(_MainTex,i.uv) : tex2D(_MainTex2,i.uv);
				*/

				/*
					式１ ? 式２ : 式３

					if(式１)
					{
						式２;
					}
					else
					{
						式３;
					}
				*/
			}
```


 - 全体スクリプト
```cs
Shader "Unlit/NewUnlitShader"
{
	Properties
	{
		_MainTex ("Texture", 2D) = "white" {}
		_MainTex2 ("Texture2",2D)= "white"{}
	}
	SubShader
	{
		Tags { "RenderType"="Opaque" }
		LOD 100
		
		// ここ重要！
		// Cull off を指定してカリングをしないように設定する
		// カリング = 描画する必要がないポリゴンを描画しないようにすること
		/* 
			オクルージョンカリング = 
			あるオブジェクトが他のオブジェクトに隠されていて現在カメラに映らないときに、
			オブジェクトのレンダリングを無効にする機能
		*/
		Cull off

		Pass
		{
			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			#pragma target 3.0
			// make fog work
			#pragma multi_compile_fog
			
			#include "UnityCG.cginc"

			struct appdata
			{
				float4 vertex : POSITION;
				float2 uv : TEXCOORD0;
			};

			struct v2f
			{
				float2 uv : TEXCOORD0;
				UNITY_FOG_COORDS(1)
				float4 vertex : SV_POSITION;
			};

			sampler2D _MainTex;
			sampler2D _MainTex2;
			float4 _MainTex_ST;
			
			v2f vert (appdata v)
			{
				v2f o;
				o.vertex = UnityObjectToClipPos(v.vertex);
				o.uv = TRANSFORM_TEX(v.uv, _MainTex);
				UNITY_TRANSFER_FOG(o,o.vertex);
				return o;
			}
			
			
			fixed4 frag(v2f i, fixed facing : VFACE) : SV_Target
			{
				return (facing > 0 ) ? tex2D(_MainTex,i.uv) : tex2D(_MainTex2,i.uv);

				/*
					if(facing > 0)
					{
						tex2D(_MainTex, i.uv);
					}
					else
					{
						tex2D(_MainTex, i.uv);
					}
					を略した書き方が
					return (facing > 0 ) ? tex2D(_MainTex,i.uv) : tex2D(_MainTex2,i.uv);
				*/

				/*
					式１ ? 式２ : 式３

					if(式１)
					{
						式２;
					}
					else
					{
						式３;
					}
				*/
			}
			ENDCG
		}
	}
}
```

[UnityのC#での呼び出しの方法](01_EX.md)
