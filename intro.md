---
layout: page
title: 计算实验
chen:
    - var: 'x;干度;x;1'
    - var: 'mu_l;液体粘度;\mu_L;Pa.s'
    - var: 'mu_g;气体粘度;\mu_G;Pa.s'
    - var: 'rho_l;液体密度;\mu_L;kg/m^3'
    - var: 'rho_g;气体密度;\mu_G;kg/m^3'
    - var: 'G;质量流量;G;kg/(m^2.s)'
    - var: 'i_fg;潜热;i_{fg};J/kg'
    - var: 'd_i;管道内径;d_i;m'
    - var: 'Pr_l;液体普朗特数;\mathrm{Pr}_L;1'
    - var: 'P_r;折算压力;P_r;1'
    - var: 'q;壁面热流密度;q;W/m^2'
    - var: 'M;相对分子质量;M_r;1'
    - var: 'm;光滑管指数;m;1'
    - formula: 'Re_l = G * d_i * (1-x) / mu_l'
    - formula: 'Xtt = ((1-x)/x)^0.875 * (mu_l/mu_g)^0.125 * (rho_g/rho_l)^0.5'
    - formula: 'h_CV = 0.023 * Re_l^0.8 * Pr_l^0.4'
    - formula: 'h_NB = 55 * q^0.67 * M^(-0.5) * P_r^m * (-log10(P_r))^(-0.55)'
    - formula: 'E = 2.35/(1/Xtt + 0.213)^0.736'
    - formula: 'Re = Re_l * E^1.25'
    - formula: 'S = 1/(1 + 2.53e-6 * Re^1.17)'
    - formula: 'h = S * h_NB + E * h_CV'
---

# Chen's correlation

<form class='form-horizontal'>

{% for item in page.chen %}{% if item.var != null %}
{% assign info = item.var | split: ";" %}
<div class='form-group'>
    <label for='{{ info[0] }}' class='col-sm-3 control-label'>{{ info[1] }} \({{ info[2] }}\)</label>
    <div class='col-sm-9'>
        {% if info[3] == '1' %}<input type='text' class='form-control' id='{{ info[0] }}'>
        {% else %}
        <div class='input-group'>
            <input type='text' class='form-control' id='{{ info[0] }}'>
            <span class='input-group-addon'>{{ info[3] }}</span>
        </div>
        {% endif %}
    </div>
</div>
{% endif %}{% endfor %}

<button class='btn btn-primary' id='calc' type='button'>计算</button>
</form>

<div id='result'></div>

<script>
$('#calc').on('click', function() {
    const parser = math.parser()
    let scope = {}
    {% for item in page.chen %}{% if item.var != null %}
    {% assign info = item.var | split: ";" %}
    scope.{{ info[0] }} = Number($('#{{ info[0] }}').val())
    {% endif %}{% endfor %}
    parser.scope = scope  // pass scope to parser
    {% for item in page.chen %}{% if item.formula != null %}
    parser.evaluate('{{ item.formula }}')
    {% endif %}{% endfor %}
    const h = parser.get('h')
    $('#result').append('<p>表面换热系数 h：' + h.toString() + 'W/m^2</p>')
})
</script>