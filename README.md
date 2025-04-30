Shader "Unlit/YunWuCloud"
{
    //  Properties 这里边的内容是暴露在 material 上的字段  进行属性更改
    Properties
    {
        _MainTex ("Cloud Texture", 2D) = "white" {}
        _Speed ("Speed", Float) = 1
        _Scale ("Scale", Float) = 1
        _Alpha ("Alpha", Range(0,1)) = 1
    }

    SubShader
    {
        Tags { "Queue"="Transparent" "RenderType"="Transparent" }
        LOD 100
        Blend SrcAlpha OneMinusSrcAlpha
        ZWrite Off
        Cull Off

        Pass
        {
            CGPROGRAM
            #pragma target 3.0
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"

            sampler2D _MainTex;
            float _Speed;
            float _Scale;
            float _Alpha;

            struct appdata
            {
                float4 vertex : POSITION;
                float2 uv : TEXCOORD0;
            };

            struct v2f
            {
                float2 uv : TEXCOORD0;
                float4 vertex : SV_POSITION;
            };

            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.uv = v.uv;
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                float2 uv = i.uv - 0.5;
                float t = _Time.y * _Speed;
                float radius = length(uv);
                float fade = smoothstep(0.4 + 0.2 * sin(t), 0.0, radius * _Scale);
                fixed4 col = tex2D(_MainTex, i.uv);
                col.a *= fade * _Alpha;
                return col;
            }
            ENDCG
        }
    }
}
